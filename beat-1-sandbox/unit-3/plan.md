# Plan: issue #60 — faithfulness checker crashes when a context chunk has `text: None`

## Diagnosis

Here's the line that breaks, at `rag/evaluator/faithfulness_checker.py:38`:

```python
context_text = " ".join([chunk.get("text", "") for chunk in context_chunks])
```

The catch is that `dict.get(key, default)` only hands back the default when the
key is **missing**. If the key is sitting right there holding `None`, you get
`None` back. So `None` ends up in the list, and `" ".join(...)` won't take it.

This is what I saw when I reproduced it. Straight from my repro comment on the
issue:

```
  File "/Users/sid/Desktop/Codepath/pathreview/rag/evaluator/faithfulness_checker.py", line 38, in check
    context_text = " ".join([chunk.get("text", "") for chunk in context_chunks])
TypeError: sequence item 0: expected str instance, NoneType found
```

The control run in that same report is the part I care about, because it's what
proves the cause. A chunk with no `text` key at all doesn't crash:

```
$ ./.venv-repro/bin/python -c "
from rag.evaluator.faithfulness_checker import FaithfulnessChecker
print('score =', FaithfulnessChecker().check('Knows Python.', [{'content': 'Python skills shown'}]))
"
score = 0.0
```

Missing key gets the `""` default and works fine. Key present holding `None`
skips the default and blows up. So the fix has to deal with the **value**, not
check whether the key exists.

## Scope

**In scope:** make a `None` value contribute nothing instead of crashing, and
drop the `xfail` marker that currently documents this bug so the test actually
guards the fix.

**Not in scope:**

- Issue #59. That's a different bug: `_is_supported()` needs two overlapping
  meaningful tokens, so short claims can never come back supported. Three tests
  in the same file (`test_partial_support_returns_middle_score`,
  `test_multiple_context_chunks`, `test_multiple_claims_varying_support`) are
  marked `xfail` for #59. I'm leaving those markers and that behaviour alone.
- Validating chunks anywhere else in the retrieval pipeline, or chasing down
  whatever is producing a `text: None` chunk upstream. This issue is about
  `check()` not crashing on what it's handed.
- Handling any non-string type. More on that under Risks.

## Files I'll touch

- `rag/evaluator/faithfulness_checker.py` — the context-building line in
  `check()`.
- `tests/unit/test_faithfulness_checker.py` — just the `xfail` marker on
  `test_none_context_chunk_text`.

## Approach

1. In `check()`, swap `chunk.get("text", "")` for `chunk.get("text") or ""`.
   That gives `""` for a missing key, for an explicit `None`, and for an empty
   string — all cases that should add no context anyway. One expression, one
   line, and nothing changes for a chunk that already has text in it.
2. Delete the `@pytest.mark.xfail(strict=True, reason="issue #60: ...")` marker
   on `test_none_context_chunk_text`. This isn't tidying up, it's required. The
   marker is `strict`, so the moment the bug is fixed that test passes when
   pytest expected it to fail, and pytest reports that `XPASS` as a failure. Fix
   the bug and leave the marker, and you've got a red suite.
3. Run the test plan below.

## Test plan

Same commands as my unit 2 reproduction, same venv (`python 3.14.5`,
`structlog 26.1.0`, `pytest 9.1.1`), run against the change.

1. The snippet from the issue body. **Before:** `TypeError` at line 38, as quoted
   above. **After:** should return a float, no traceback.

   ```
   ./.venv-repro/bin/python -c "
   from rag.evaluator.faithfulness_checker import FaithfulnessChecker
   print('score =', FaithfulnessChecker().check('Knows Python.', [{'text': None}]))
   "
   ```

2. Both controls from the repro report, unchanged. **Before:** both printed
   `score = 0.0`. **After:** both should still print `score = 0.0`. That's how
   I'll know I didn't break anything that already worked.

3. The named test. **Before:** `XFAIL`. **After:** should be `PASSED`, with the
   marker gone.

   ```
   ./.venv-repro/bin/python -m pytest "tests/unit/test_faithfulness_checker.py::TestFaithfulnessChecker::test_none_context_chunk_text" -v
   ```

4. The whole file, to check my not-in-scope claim actually held. **Expect:** the
   three issue-#59 tests still `XFAIL` and nothing else moves.

   ```
   ./.venv-repro/bin/python -m pytest tests/unit/test_faithfulness_checker.py -v
   ```

## Risks and unknowns

- **A truthy non-string value still crashes.** `chunk.get("text") or ""` handles
  falsy stuff, so `{"text": 0}` and `{"text": []}` become `""`. But something
  like `{"text": 123}` is truthy, so it goes straight into `" ".join(...)` and
  raises the same `TypeError`. I'm deliberately not going with
  `str(chunk.get("text") or "")`, because the issue is about `None` and a
  blanket `str()` would quietly stringify genuinely broken data instead of
  letting me see it. If a reviewer would rather have the wider version, it's a
  one-word change and I'll do it.
- **I haven't traced where a `text: None` chunk comes from.** This fix makes
  `check()` safe against the input it gets. Whether something upstream in
  retrieval shouldn't be emitting that chunk in the first place is a separate
  question, and I'd rather file it as a follow-up than widen this change.
- **My Python is newer than the project targets.** I'm on 3.14.5;
  `pyproject.toml` says `requires-python = ">=3.11"` and mypy targets 3.11. The
  bug and the fix are both plain `str`/`None` handling with nothing
  version-specific about them, but I haven't run the suite on 3.11.

## Deviations

Nothing deviated. I built the two changes this plan named, in the order it named
them, and the test plan gave me the results it predicted.

Being specific, since "no deviations" is cheap to write and hard to believe:

- The code change is the exact expression I said I'd use:
  `chunk.get("text", "")` became `chunk.get("text") or ""` at
  `rag/evaluator/faithfulness_checker.py:38`. One line, nothing else in that
  file.
- The test change is the one I said I'd make: the four marker lines
  `@pytest.mark.xfail(strict=True, reason="issue #60: ...")` gone from
  `test_none_context_chunk_text`. The whole diff is `1 insertion, 5 deletions`
  across those two files.
- All four test-plan steps came back the way I expected, including the one I was
  least sure about. The full-file run reports `19 passed, 3 xfailed`, so the
  three issue-#59 markers are untouched and my not-in-scope claim held in
  practice, not just on paper.

Two things I'd flagged as risks stayed risks, and I want to say so rather than
let anyone assume I quietly sorted them out. `{"text": 123}` still raises the
same `TypeError` — I didn't widen the fix to `str()` coercion, exactly as I said
I wouldn't without a reviewer asking. And I still haven't tracked down what
produces a `text: None` chunk upstream, so that's still a follow-up and not
something this change answers.
