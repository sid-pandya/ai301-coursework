# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of my claim and reproduction on the issue I chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`.

---

## Your identity upstream

**GitHub username**

sid-pandya

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/60#issuecomment-5864244366

Hello Team,
I'd like to take this one on as my first contribution to this repo.

To be specific about what I am picking up: `FaithfulnessChecker.check()` builds
its context with `chunk.get("text", "")`, and the issue's reading is that the
default only covers a *missing* key, so a chunk carrying an explicit
`"text": None` reaches `" ".join(...)` and raises `TypeError`. The issue also
points at `test_none_context_chunk_text` in
`tests/unit/test_faithfulness_checker.py`, which is currently marked
`xfail(strict=True)` against this issue number.

What I am going to do next, in order:

1. Set up the project locally and run the three-line snippet from the issue body
   against a clean checkout.
2. Run that named test and record what pytest reports for it.
3. Check the neighbouring case where the `text` key is absent entirely, since if
   that one returns a score normally, it confirms the cause is the `.get()`
   default rather than the join itself.

I will post a reproduction report with my environment and the exact output
before I propose any change. To be clear about scope: I am promising the
investigation and the report, not a fix or a timeline — if the reproduction
turns up something different from what the issue describes, I will post that
instead.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/60#issuecomment-5864274547

Reproduced. The `TypeError` happens exactly where the issue says it does, and a
control run isolates the cause to the `.get()` default rather than the join.

**Environment**

- Repo at commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088` (`main`, 2026-09-16), fresh `--depth 1` clone
- Python 3.14.5, macOS (arm64)
- structlog 26.1.0, pytest 9.1.1, installed into a clean venv

Two honest notes on this environment. First, Python 3.14.5 is newer than what
the project targets (`requires-python = ">=3.11"`, and mypy is pinned to
`python_version = "3.11"`), so this is above the target rather than on it; the
failure is a plain `str`/`None` type error and is not version-specific, but it is
worth saying. Second, I did not run the full `docker compose up -d && make setup`
stack. `rag/evaluator/faithfulness_checker.py` imports only `re` and `structlog`,
so the failing path needs no database, no services, and no migrations. A clean
venv with those two packages is enough to reach it, which is why the steps below
are short.

**Steps**

Clone the repo, then from the repo root:

```
python3 -m venv .venv-repro
./.venv-repro/bin/pip install "structlog>=24.1.0" "pytest>=7.4.0"
```

Then run the snippet from the issue body:

```
./.venv-repro/bin/python -c "
from rag.evaluator.faithfulness_checker import FaithfulnessChecker
FaithfulnessChecker().check('Knows Python.', [{'text': None}])
"
```

**Actual output**

```
  File "<string>", line 3, in <module>
    FaithfulnessChecker().check('Knows Python.', [{'text': None}])
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/Users/sid/Desktop/Codepath/pathreview/rag/evaluator/faithfulness_checker.py", line 38, in check
    context_text = " ".join([chunk.get("text", "") for chunk in context_chunks])
TypeError: sequence item 0: expected str instance, NoneType found
```

**Expected:** `check()` returns a float in `0.0-1.0`, as it does for every other
chunk shape.

**Actual:** it raises `TypeError: sequence item 0: expected str instance,
NoneType found` at `faithfulness_checker.py:38`, which is the line and the
exception the issue names.

**Control runs, to isolate the trigger**

Same call, same environment, only the chunk shape changes.

A normal string chunk — no crash:

```
$ ./.venv-repro/bin/python -c "
from rag.evaluator.faithfulness_checker import FaithfulnessChecker
print('score =', FaithfulnessChecker().check('Knows Python.', [{'text': 'Python skills shown'}]))
"
2026-09-27 22:03:47 [info     ] faithfulness_checked           claims_count=1 score=0.0 supported_count=0
score = 0.0
```

A chunk with the `text` key **missing entirely** — also no crash:

```
$ ./.venv-repro/bin/python -c "
from rag.evaluator.faithfulness_checker import FaithfulnessChecker
print('score =', FaithfulnessChecker().check('Knows Python.', [{'content': 'Python skills shown'}]))
"
2026-09-27 22:03:47 [info     ] faithfulness_checked           claims_count=1 score=0.0 supported_count=0
score = 0.0
```

That second control is the one that matters. `{"content": ...}` has no `text`
key at all, so `chunk.get("text", "")` returns the `""` default and the join
succeeds. `{"text": None}` *has* the key, so `.get()` returns the stored `None`
and the default never applies. The cause is the `.get()` default not covering a
present-but-null value, not the `" ".join(...)` itself — the join is only where
it surfaces.

**The named test**

```
$ ./.venv-repro/bin/python -m pytest "tests/unit/test_faithfulness_checker.py::TestFaithfulnessChecker::test_none_context_chunk_text" -v
============================= test session starts ==============================
platform darwin -- Python 3.14.5, pytest-9.1.1, pluggy-1.6.0
rootdir: /Users/sid/Desktop/Codepath/pathreview
configfile: pyproject.toml
collecting ... collected 1 item

tests/unit/test_faithfulness_checker.py::TestFaithfulnessChecker::test_none_context_chunk_text XFAIL [100%]

============================== 1 xfailed in 0.05s ==============================
```

`XFAIL` here means the test failed as its `xfail(strict=True)` marker expects, so
the bug is present at this commit. Because the marker is `strict`, that same test
will turn into an `XPASS` failure once the behaviour is fixed, which makes it a
usable signal for a fix rather than something that needs rewriting.

One thing I noticed but am not treating as part of this issue: the sibling test
`test_missing_text_key_in_chunk` covers the `{"content": ...}` case my control B
uses and is not marked `xfail`, which lines up with control B passing. I mention
it only because it confirms the two cases are already understood as separate in
the test suite.

Disclosure: my workflow on this course is AI-assisted (Claude Code). I ran every
command shown above myself and the output pasted here is what my terminal
printed.

## Eval iterations

**Run history**

Four runs, in order. The two partial runs were `--only` re-grades, which the
harness refuses to save; the two full runs are the scoreable ones, and the last
is the run committed as `eval-run.txt`.

1. Partial smoke run, `--only pkg-20,pkg-02,pkg-19,pkg-01`, one package from
   each of the categories I was least sure my checks could see, run before
   spending on a full grade: `agreement: 4/4 scored items`
2. First full run: `agreement: 16/20 scored items  (bar: 18/20: below the bar)`
   with
   `categories: clear-accept 4/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`.
   The floor already held, including the single-package `disclosure` category.
   All four misses were false rejects on packages whose gold label was `accept`:
   pkg-03 on `outcome-stated-honestly`, pkg-05 on `steps-followable` and
   `conventions-and-disclosure`, and pkg-09 and pkg-10 both on
   `behavior-matches-issue`.
3. Partial re-grade after revising four checks,
   `--only pkg-03,pkg-05,pkg-09,pkg-10,pkg-02,pkg-16,pkg-04,pkg-13,pkg-18,pkg-20`
   — the four misses plus six canaries, one from each category a loosened check
   could have flipped: `agreement: 10/10 scored items`
4. Confirming full run, saved with `--save-run`:
   `agreement: 20/20 scored items  (bar: 18/20: PASS)` with
   `categories: clear-accept 8/8  disclosure 1/1  no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`

The last score is the agreement line in the committed `eval-run.txt`.

**Package analysis**

`pkg-10` (starship, a module vanishing from the prompt).

Gold label: `accept`. My rubric graded it `reject` on the first full run. My
committed rubric now grades it `accept`, and the reason it moved is a structural
hole rather than a threshold being slightly off.

The package is an honest cannot-reproduce. The author set the repo up, tried the
reported scenario, tried several variations of it, and got the correct behaviour
every time. The evidence the skill recorded against my check was:

> Issue reports a vanished module and starship explain omitting it; the artifact
> shows the module rendering and starship explain listing it — no artifact shows
> the reported symptom.

That is a completely accurate reading of what I had written. My
`behavior-matches-issue` pass condition required that "an artifact shows the
same symptom the issue describes". A report that faithfully documents *not*
seeing the symptom can never satisfy that sentence. So the check did not
mis-measure pkg-10; it structurally excluded an entire legitimate outcome, and
it did the same thing to pkg-09 for the same reason. Two of my four misses were
one mistake made once.

What I had conflated is *reading the artifact correctly* with *the artifact
showing the bug*. Those come apart precisely in the cannot-reproduce case, which
is the case this unit says earns full marks when it is evidenced. The revision
gave the check two ways to pass and kept one way to fail: a mismatch the report
does not notice.

**Check rationale**

The check, quoted exactly as it reads now in the `rubric.md` uploaded to
`tools/repro-check/`:

> | `behavior-matches-issue` | The report's artifacts, output excerpts, logs, or described observations, read line by line against the specific symptom the issue names, together with what the report says those artifacts are. | The report reads its own artifacts correctly against the issue. Two ways to pass: an artifact shows the same symptom the issue describes, the same error or crash, the same wrong value, the same missing or extra output; or the report states that it did not get the reported symptom and shows what it got instead. What fails is a mismatch the report does not notice: presenting an artifact as the issue's symptom when it is a different failure mode, such as a setup error, a validation message where the issue reports a crash, the right symptom from an input the issue does not name, or a related-but-distinct bug. A correctly labelled non-reproduction is not a mismatch; it is a result. | required |

Two things in that form are deliberate.

The clause "together with what the report says those artifacts are" is the whole
repair. The check no longer asks what the artifact shows in isolation; it asks
whether the report's account of the artifact is right. That single move admits
the honest negative and still rejects pkg-02, where a clean
`error: Invalid value for '--line-range'` and `echo $?` returning `1` is
presented as a confirmed capacity-overflow panic. The artifact is real in both
cases; the difference is entirely in whether the prose describes it correctly.

The closing sentence, "A correctly labelled non-reproduction is not a mismatch;
it is a result", is there because the first version's failure was one of
omission rather than of wording. I could have added "unless the report says it
could not reproduce" as an exception and scored the same, but an exception reads
as a special case, and this is not a special case: it is the second normal
outcome of trying to reproduce something. Naming it as a result rather than as
an exemption is what stops me from re-tightening the check the next time I read
it.

**Trade-offs**

What this check now gives up is the ability to audit diligence on a negative
result. Because it trusts the report's own labelling, a report that says "I could
not reproduce this" and shows some output passes, whether the author probed hard
or gave up after one run on the wrong version. I cannot see effort from the
artifacts, so I do not pretend to grade it. What I do still catch is the
dangerous direction: a confident mislabel, where an adjacent failure is offered
as the bug. That asymmetry is intentional, because a false confirmation sends a
maintainer down the wrong path, while a lazy non-reproduction mostly wastes only
the author's own time.

I did not take the loosening on trust. Because this revision and three others
all widened their pass conditions, I re-ran the four fixed packages together
with six canaries in one `--only` call: pkg-02 and pkg-16 for `wrong-target`,
the category this check could most easily have flipped, pkg-04 and pkg-13 for
`no-evidence`, pkg-18 for `unfollowable-comms`, and pkg-20 for `disclosure`.
pkg-20 was the one that actually mattered. `disclosure` is a single-package
category, so a flip there would have taken the category floor down and no amount
of agreement elsewhere could have bought it back — and I had just narrowed
`conventions-and-disclosure` to stop it failing pkg-05 over a bug-report
template. All ten agreed, `disclosure` stayed 1/1, and that partial run cost
about two dollars instead of discovering the problem on a four-dollar
confirming run.

---

Related paths: `eval-run.txt` in this directory; my skill's files in
`tools/repro-check/`.
