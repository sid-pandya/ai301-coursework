# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

My plan, the branch I built it on, and the eval runs behind `eval-run.txt`.

---

## Posted upstream

**GitHub username**

sid-pandya

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/60#issuecomment-5988608139

Hey everyone, my reproduction aligns with the thread, so here is my plan to fix this:

*   **The Bug:** At `faithfulness_checker.py:38`, `.get("text", "")` returns `None` if the `"text"` key is explicitly set to `None` (which breaks the `.join`). 
*   **The Fix:** I'll update it to `chunk.get("text") or ""` to safely handle `None`, missing keys, and empty strings in one go.
*   **Tests & Scope:** I will remove the `xfail(strict=True)` marker from `test_none_context_chunk_text` so it cleanly passes. I am strictly leaving Issue #59 and its related `xfail` tests alone.

I'll report back with the before-and-after test outputs once I build it!

---

## Your branch

**Branch**

`fix/60-none-chunk-text`

**Evidence**

My unit 2 repro steps, re-run against the change I built. Same venv both times:
Python 3.14.5, structlog 26.1.0, pytest 9.1.1, macOS (arm64).

**Before** — the snippet from the issue body, on unchanged `main` at commit
`2f4e82f52efbcfcc57d65b3fa5348672163ca088`. This is what I posted in my unit 2
repro comment:

```
$ ./.venv-repro/bin/python -c "
from rag.evaluator.faithfulness_checker import FaithfulnessChecker
FaithfulnessChecker().check('Knows Python.', [{'text': None}])
"
  File "<string>", line 3, in <module>
    FaithfulnessChecker().check('Knows Python.', [{'text': None}])
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/Users/sid/Desktop/Codepath/pathreview/rag/evaluator/faithfulness_checker.py", line 38, in check
    context_text = " ".join([chunk.get("text", "") for chunk in context_chunks])
TypeError: sequence item 0: expected str instance, NoneType found
```

Before, the two controls from that same report. These are the ones that pin the
cause on the `.get()` default rather than on the join:

```
$ ./.venv-repro/bin/python -c "
from rag.evaluator.faithfulness_checker import FaithfulnessChecker
print('score =', FaithfulnessChecker().check('Knows Python.', [{'text': 'Python skills shown'}]))
"
score = 0.0

$ ./.venv-repro/bin/python -c "
from rag.evaluator.faithfulness_checker import FaithfulnessChecker
print('score =', FaithfulnessChecker().check('Knows Python.', [{'content': 'Python skills shown'}]))
"
score = 0.0
```

Before, the named test, which had a `strict` `xfail` marker pointing at this
issue:

```
$ ./.venv-repro/bin/python -m pytest "tests/unit/test_faithfulness_checker.py::TestFaithfulnessChecker::test_none_context_chunk_text" -v
tests/unit/test_faithfulness_checker.py::TestFaithfulnessChecker::test_none_context_chunk_text XFAIL [100%]

============================== 1 xfailed in 0.05s ==============================
```

**After** — same four things on branch `fix/60-none-chunk-text` at commit
`41eb08d`. The whole diff is one expression plus four deleted marker lines:

```
$ git diff --stat HEAD~1
 rag/evaluator/faithfulness_checker.py   | 2 +-
 tests/unit/test_faithfulness_checker.py | 4 ----
 2 files changed, 1 insertion(+), 5 deletions(-)
```

The snippet that used to raise now returns a score:

```
$ ./.venv-repro/bin/python -c "
from rag.evaluator.faithfulness_checker import FaithfulnessChecker
print('score =', FaithfulnessChecker().check('Knows Python.', [{'text': None}]))
"
2026-10-04 21:07:52 [info     ] faithfulness_checked           claims_count=1 score=0.0 supported_count=0
score = 0.0
```

Both controls, unchanged, so I know the fix didn't move anything that already
worked:

```
$ ./.venv-repro/bin/python -c "
from rag.evaluator.faithfulness_checker import FaithfulnessChecker
print('control A (string) score =', FaithfulnessChecker().check('Knows Python.', [{'text': 'Python skills shown'}]))
print('control B (key absent) score =', FaithfulnessChecker().check('Knows Python.', [{'content': 'Python skills shown'}]))
"
control A (string) score = 0.0
control B (key absent) score = 0.0
```

The named test, now that the marker's gone:

```
$ ./.venv-repro/bin/python -m pytest "tests/unit/test_faithfulness_checker.py::TestFaithfulnessChecker::test_none_context_chunk_text" -v
tests/unit/test_faithfulness_checker.py::TestFaithfulnessChecker::test_none_context_chunk_text PASSED [100%]

============================== 1 passed in 0.03s ===============================
```

And the whole file, which is how I checked my not-in-scope claim. The three
`xfail` markers for issue #59 are still `xfail`, so nothing outside this issue
moved:

```
$ ./.venv-repro/bin/python -m pytest tests/unit/test_faithfulness_checker.py
tests/unit/test_faithfulness_checker.py ..x...x......x........           [100%]

======================== 19 passed, 3 xfailed in 0.04s =========================
```

## Eval iterations

**Run history**

Four runs, in order. The partial ones are `--only` re-grades and the harness
won't save those, so only the full runs could become the committed file.

1. Partial smoke run, `--only pkg-04,pkg-20,pkg-02,pkg-10`. I ran this before
   paying for a full grade because it covers both packages in the two-package
   `thread-convention` category, plus one clear accept and one unbuildable:
   `agreement: 4/4 scored items`
2. First full run: `agreement: 19/20 scored items  (bar: 18/20: PASS)` with
   `categories: clear-accept 6/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`.
   Over the bar with every category matched, first try. The one miss was pkg-09,
   a `clear-accept` that my rubric rejected on `thread-aligned`.
3. Partial re-grade while I tried rewording that check,
   `--only pkg-09,pkg-04,pkg-20`. That's the miss plus both
   `thread-convention` packages as canaries, because loosening `thread-aligned`
   is exactly the change that could flip them: `agreement: 2/3 scored items`.
   The canaries held at `thread-convention 2/2`, but pkg-09 didn't flip, so the
   reword got me nothing and I reverted it.
4. Confirming full run on the reverted rubric, with `--save-run`:
   `agreement: 19/20 scored items  (bar: 18/20: PASS)` with
   `categories: clear-accept 6/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`

That last score is the agreement line in the `eval-run.txt` I committed.

**Package analysis**

`pkg-09` — ripgrep, glob patterns not matching on Windows because of the path
separator.

Gold label says `accept`. My rubric said `reject`. It's the one package I still
disagree with the answer key on after four runs. The check that failed it was
`thread-aligned`, and here's the evidence my skill recorded:

> Thread records tmccombs (COLLABORATOR) naming a third, already-implemented fix
> variant ('a third variant a contributor has since implemented'); the plan and
> comment engage options 1 and 2 by name but never mention this third variant at
> all, despite showing the same duplicate-work awareness for PR #2089.

That's a fair reading of what I wrote, which is the annoying part. The thread has
a COLLABORATOR listing three fix options. The plan takes option 2, says no to
option 1 and why, and engages the contributor's PR #2089 — but never mentions
option 3. My pass condition says the plan has to engage with maintainer
direction by "adopting it, or departing from it and saying why", and my check
read every option the maintainer listed as direction that needed an answer.

I think the gold label has it right, and I can say why. Option 3 shows up in the
thread as nothing more than "a third variant a contributor has since
implemented" — no mechanism, no link, nothing there to adopt or turn down.
Engaging with a maintainer's direction should mean engaging with the direction,
not ticking off every item in a list they happened to write. A plan that picks
one of three options and explains why not the ones it can actually see has done
the thing this check exists for.

So it's real over-strictness, but narrow, and I decided to live with it. How I
tested that decision instead of just assuming it is in Trade-offs.

**Check rationale**

The check, quoted exactly as it reads in the `rubric.md` I uploaded to
`tools/plan-check/`:

> | `thread-aligned` | The thread-highlights section (live: the issue thread), specifically any comment from an OWNER, MEMBER, COLLABORATOR or CONTRIBUTOR that names a cause, proposes or rejects an approach, or reports a fix already in progress; read against the plan's diagnosis, scope and approach. | Where the thread carries maintainer direction, the plan engages with it: adopting it, or departing from it and saying why. What fails is a plan that contradicts a stated maintainer position without acknowledgement, proposes work a maintainer already rejected, or ignores a fix a maintainer has in flight and plans around it as if the thread were empty. Where the thread carries no maintainer direction, this check passes; and a plan may not claim thread support the thread does not contain. | required |

Three things in there are on purpose, and one of them is a decision I made and
then took back.

The evidence column spells out the author associations instead of just saying
"the maintainers", because whatever is executing this has to tell direction
apart from noise. My own issue is a good example: the thread has fourteen
comments and every single one is a classmate with association `NONE`. Without
the association filter, a grader could read fourteen comments as a thread full
of direction and fail any plan that didn't answer all of them.

The last clause — "a plan may not claim thread support the thread does not
contain" — is there because this failure runs both ways. pkg-20's plan cites
"the direction already given in the thread" and an approach it says was
"already rejected as too expensive", and in that case the thread does say those
things, so it passes. But the clause is what stops a plan borrowing authority it
hasn't got, which is the mirror image of pkg-04 planning docs-only work while
the OWNER has a patched binary in flight.

The decision I took back: after run 2 I rewrote this pass condition as a closed
list of three failure modes, with a sentence saying that adopting one of several
offered options counts as engagement and the plan "need not address every
alternative the maintainer listed". That was aimed straight at pkg-09. It didn't
work — pkg-09 still came back `reject` on run 3 — so the reword was costing me
precision in the one category most likely to be weakened by it and buying zero
agreement. I reverted to the wording above.

**Trade-offs**

What this check gives up is pkg-09's kind of plan: one that takes a maintainer's
option and reasons about the alternatives it can actually act on, while saying
nothing about one that's described too vaguely to act on. I accept it'll fail
those, and I know the cost is exactly one package out of twenty, because the run
table tells me so.

I didn't just accept that on a hunch. When I tried the looser wording I re-ran
it with `--only pkg-09,pkg-04,pkg-20` — the miss, plus one canary from each of
the two `thread-convention` packages. That category only has two members and
`thread-aligned` is the check both of them turn on, so a flip there takes the
category floor down and no amount of agreement elsewhere buys it back. Result:
`thread-convention 2/2`, canaries held, pkg-09 still `reject`. That's what
decided it. A loosening that keeps all of its risk and delivers none of its
benefit isn't a trade-off, it's just a weaker check, so I reverted and the
committed run uses the stricter wording.

And the cost that's left sits where I'd rather have it. A plan that talks past a
maintainer wastes their time and mine. A plan that gets held because it didn't
mention an option nobody actually described is a false alarm I can spot in one
line of output and overrule myself.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; my skill's files in
`tools/plan-check/`.
