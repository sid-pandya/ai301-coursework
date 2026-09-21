# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/60

**Verdict output**

Live-mode run over three candidates (#68, #60, #56), pasted verbatim. The skill
accepted all three and ranked them against the fit profile in `scope.md`; #60
ranked first and is the issue I am carrying into Unit 2. Its per-issue verdict in
the JSON block below is `accept`.

````
## Grading summary

**Repo-level facts (apply to all three):** `codepath/pathreview-ai301-fa26-s3` — not archived, newest commit 2026-09-16 (4 days old), no releases ever published, `docs/CONTRIBUTING.md` has no AI-use policy (silence), and a sampled issue (#52) got a COLLABORATOR reply within 6 days.

All three issues are opened by `Aburke225` (COLLABORATOR), carry `bug` + `good first issue` + `tier-1` labels, have no assignee, and have zero linked/mentioned PRs (no abandoned attempts). All three **pass every required check → accept**.

Ranked by your fit profile (Python backend, retrieval/ingestion/test-suite, smallest single-function fix, avoid open design):

1. **#60 — Faithfulness checker crashes on `text: None`** (RAG). Smallest possible fix: `chunk.get("text", "")` doesn't cover the case where the key exists but is `None`, causing a `TypeError` in a `" ".join(...)`. A failing test (`test_none_context_chunk_text`) already pins the exact behavior. No comments, no competing claim. This is as close to "one wrong line, one clear right answer" as it gets.
2. **#68 — Keyword search `ZeroDivisionError` on empty index** (retrieval). Also retrieval-domain and bounded, but slightly larger (estimated 2–4h, touches `index()` plus removing an `xfail` marker in the test), and a classmate (`acordero4852`, NONE association) already commented claiming it — harmless per the Path Review house rule, but it's not as clean a "no one's touched it" pick as #60.
3. **#56 — Structural chunker drops headingless docs** (ingestion). Matches your ingestion interest, but the fix requires a judgment call the issue doesn't fully settle (process as one block vs. an alternative chunking strategy) — a bit more design than a pure "wrong behavior, one right answer" fix, so it ranks last among three otherwise-comparable accepted issues.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/60",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit 2026-09-16T21:42:18Z, within 180 days of today (2026-09-20)."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repo API field archived: false."},
      {"name": "issue-unclaimed", "grade": "pass", "evidence": "assignees: none; 0 comments on the issue."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "Search API for PRs referencing #60 returns total_count: 0."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Body describes a single-method bug (`chunk.get(\"text\", \"\")` doesn't handle an explicit None value) with a named failing test (`test_none_context_chunk_text`) demonstrating it."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Labels: bug, good first issue, rag, tier-1; opened by Aburke225, author_association: COLLABORATOR."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "docs/CONTRIBUTING.md contains no AI-use language at all; silence passes."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Sampled issue #52 got a COLLABORATOR (Aburke225) reply 6 days after opening."},
      {"name": "released-recently", "grade": "fail", "evidence": "GET /releases returns an empty array — no release has ever been published."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Labels include 'good first issue'."}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/68",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit 2026-09-16T21:42:18Z, within 180 days of today (2026-09-20)."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repo API field archived: false."},
      {"name": "issue-unclaimed", "grade": "pass", "evidence": "assignees: none; one comment from classmate acordero4852 (association NONE) claiming the work on 2026-09-19 — Path Review house rule: other students' claim comments do not block."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "Search API for PRs referencing #68 returns total_count: 0; issue timeline shows no linked PRs."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Body names the exact cause (BM25Okapi ZeroDivisionError on empty tokenized corpus), the two files to touch, and an estimated effort of 2-4 hours."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Labels: bug, good first issue, rag, tier-1; opened by Aburke225, author_association: COLLABORATOR."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "docs/CONTRIBUTING.md contains no AI-use language at all; silence passes."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Sampled issue #52 got a COLLABORATOR (Aburke225) reply 6 days after opening."},
      {"name": "released-recently", "grade": "fail", "evidence": "GET /releases returns an empty array — no release has ever been published."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Labels include 'good first issue'."}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/56",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit 2026-09-16T21:42:18Z, within 180 days of today (2026-09-20)."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repo API field archived: false."},
      {"name": "issue-unclaimed", "grade": "pass", "evidence": "assignees: none; 0 comments on the issue."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "Search API for PRs referencing #56 returns total_count: 0."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Body describes a single method (`StructuralChunker.chunk()`) returning zero chunks for headingless docs, with a concrete repro (a ~1000-char plain document producing zero chunks)."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Labels: bug, good first issue, ingestion, tier-1; opened by Aburke225, author_association: COLLABORATOR."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "docs/CONTRIBUTING.md contains no AI-use language at all; silence passes."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Sampled issue #52 got a COLLABORATOR (Aburke225) reply 6 days after opening."},
      {"name": "released-recently", "grade": "fail", "evidence": "GET /releases returns an empty array — no release has ever been published."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Labels include 'good first issue'."}
    ],
    "verdict": "accept"
  }
]
```
````

---

## Eval iterations

**Run history**

Four runs, in order. The two partial runs were `--only` re-grades, which the
harness refuses to save; the two full runs are the scoreable ones, and the last
is the run committed as `eval-run.txt`.

1. Partial smoke run, `--only issue-12,issue-14,issue-20`, chosen to test my
   three least certain checks before paying for a full run:
   `agreement: 3/3 scored items`
2. First full run: `agreement: 16/20 scored items  (bar: 18/20: below the bar)`.
   Four misses, all of them false rejects on issues whose gold label was
   `accept`: issue-01, issue-04 and issue-19 failed `scope-bounded`, and
   issue-09 failed `issue-unclaimed`. The category floor already held here
   (`categories: claimed 4/4  clear-accept 4/8  dead-repo 3/3  policy 1/1  scope 4/4`),
   so the problem was precision on the accepts, not a blind spot in a category.
3. Partial re-grade after rewording those two checks,
   `--only issue-01,issue-04,issue-09,issue-19,issue-10,issue-12,issue-15` —
   the four misses plus three canaries that had to stay rejects:
   `agreement: 7/7 scored items`
4. Confirming full run, saved with `--save-run`:
   `agreement: 20/20 scored items  (bar: 18/20: PASS)` with
   `categories: claimed 4/4  clear-accept 8/8  dead-repo 3/3  policy 1/1  scope 4/4`

The last score, 20/20, is the agreement line in the committed `eval-run.txt`.

**Issue analysis**

`issue-04` (zxcalc/zxlive#555, "Missing several basic rule previews").

Gold label: `accept`. My committed rubric also grades it `accept`, but it did not
at first, and the reason it changed is the useful part.

In run 2 my rubric rejected it. The failing check was `scope-bounded`, and the
evidence the skill recorded was:

> body: 'Including remove identity, fuse spiders, remove self loops, etc.' —
> open-ended list of sub-items, not one bounded change

That is a faithful reading of what I had written. My pass condition said the
issue must not be "an umbrella or tracking issue listing sub-items to be split
up", and the body of issue-04 does literally list items. But the check was
measuring the wrong thing. Those three named items are three instances of one
repetitive change — adding missing rule previews — which one pull request can
land together. A real umbrella issue is issue-10, whose body points at roughly
130 other issue numbers, each of which is its own separate piece of work. My
wording could not tell those two apart, because it keyed on the presence of a
list rather than on whether the listed things are separate deliverables.

This is also exactly the failure the evidence guide warns about: "Short is not
the same as unscoped... Grade the size of the work being asked for, not the
polish of the writeup." I had read that and still wrote a check that graded the
shape of the prose. Rewording it to fail only on a tracking list that *points at
other issues or pull requests* fixed issue-04, issue-01 and issue-19 at once,
and left issue-10 rejected.

**Check rationale**

The check, quoted as it is currently written in the `rubric.md` uploaded to
`tools/issue-select/`:

> | `scope-bounded` | The issue title and body, and the comment thread. | The issue asks for work one newcomer could land in a single pull request. It fails on exactly three things: the body is a tracking list that points at other issues or pull requests to be completed separately; it is a pure usage or support question rather than a change to the project; or a maintainer says in the thread that the fix needs changes to core internals, or that the design is still unsettled. Nothing else fails it. Several examples of one repetitive change, an acceptance-criteria checklist, a bare title, or a body with no reproduction steps are all bounded: grade the size of the work asked for, not the length or polish of the writeup. | required |

Two deliberate choices in that form.

First, it enumerates a closed list of failure conditions and then says "Nothing
else fails it." My first version described what a bounded issue looks like and
left the grader to decide what counted as a violation, and the grader read any
enumeration in a body as disqualifying. Scope is the one check here that cannot
be reduced to a number, so the discipline has to come from making the fail set
exhaustive rather than from a threshold.

Second, the closing sentence names the specific shapes that must *not* fail —
repetitive changes, checklists, bare titles, missing repro steps. That sentence
exists because each of those had actually cost me a false reject. It is cheaper
to state the exclusions than to hope the grader infers them.

**Trade-offs**

What this check gives up is the ability to see difficulty that the issue text
does not admit to. It grades the work as described. An issue can read as one
tidy bounded change and still turn out to touch core internals, and unless a
maintainer says so in the thread, this check will pass it. I accept that: the
alternative is guessing at hidden difficulty from prose, which is what the first
version effectively did, and it cost three false rejects out of eight accepts.

I did not take that on trust. When I loosened the wording I re-ran `issue-10`
as a canary with `--only`, because it is the one gold reject that depends on
this check alone — its repo is active, it is unassigned, it has no open linked
PRs and its policy only sets conditions, so if `scope-bounded` stopped catching
it, it would have flipped to a false accept and I would have traded four misses
for one. It stayed `reject`, on the tracking-list clause, and `issue-15` and
`issue-12` also held. That is what made the reword safe rather than merely
better-scoring.

The residual cost is carried instead by `no-abandoned-attempts`, which catches
the hidden-difficulty case from the outside: two or more closed unmerged linked
PRs is the repo telling me the work is harder than it looks, without my having
to judge the prose at all.

---

## Selection rationale

**Selection rationale**

*1. Fit to my interests and to the time available.*

I work in Python, and most of what I have built is retrieval-augmented
generation plumbing — embeddings, a Chroma vector store, chunking PDFs on the
way in. Issue #60 sits exactly there: `FaithfulnessChecker.check()` in the RAG
evaluator. It is also about as small as a real bug gets. The body names the
cause in one sentence (`chunk.get("text", "")` returns `None` when the key
exists with a `None` value, so the following `" ".join(...)` raises
`TypeError`), gives a three-line reproduction, and names the failing test that
already covers it, `test_none_context_chunk_text`. Unit 2 asks me to reproduce
the issue before fixing it, and here the reproduction is handed to me, which is
what makes this fit the time I actually have rather than the time I would like
to have.

*2. What the verdict identified correctly, and what I weighed that the rubric
could not.*

The verdict got the mechanical facts right, and I checked them against the API
rather than taking them on trust: unassigned, zero comments, no linked pull
requests, `bug` + `good first issue` + `rag` + `tier-1` labels, opened by a
COLLABORATOR, repo last pushed four days before I ran this, and no AI policy in
`docs/CONTRIBUTING.md`. It was also right to rank #68 below #60 on the strength
of a classmate's existing claim comment while correctly refusing to let that
claim block the issue, which is the Path Review house rule working as written.

Three things I weighed that the rubric had no way to see. The first is that this
repo is a classroom fixture, so `maintainer-responsive` and `released-recently`
are close to meaningless here — the repo has never published a release, which my
rubric dutifully graded `fail` on all three candidates, and in a normal repo
that would mean something. It only did not sink them because I had already made
that check `preferred`. The second is that a `tier-1` label means course staff
pre-sized the work, a much stronger signal than anything my rubric derives from
the issue text, and one my checks are blind to. The third is the thing that
actually decided it between #60 and #56: #56 leaves a design choice open —
whether a headingless document should become one block or be chunked another way
— and my `scope-bounded` check passed it because no maintainer had said the
design was unsettled. I could see that it was. The rubric grades what the thread
states; I could read what the thread left out.

*3. The anticipated difficulty in claiming it.*

Low, and mostly not about the claim. The issue has no assignee and no comments,
so nothing has to be negotiated, and the house rule means a classmate arriving
on the same issue costs neither of us anything. The one risk worth naming is
that #60 is visibly the easiest issue in the tier-1 set, which makes it the one
most likely to attract several classmates at once. If that happens I will still
open my pull request, since course credit attaches to the pull request rather
than to the merge.

The real difficulty is downstream. The fix itself is close to one line, and a
one-line fix is easy to submit badly — the change that matters is making sure
`test_none_context_chunk_text` genuinely fails before the fix and passes after,
and that I have not simply papered over the `None` with an `or ""` in a way that
hides a bad chunk elsewhere in the pipeline. I would rather spend that effort on
a small change I can fully explain than on a larger one I cannot.

---

Related paths: `eval-run.txt` in this directory; my skill's files in
`tools/issue-select/`.
