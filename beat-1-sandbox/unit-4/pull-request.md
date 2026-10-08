# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request I opened against the Path Review repo, and of the eval runs
behind `eval-run.txt`.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/94

**Branch**

`fix/60-none-chunk-text`

## Eval iterations

**Run history**

Two runs.

1. Smoke run, `--only pkg-01,pkg-20,pkg-02,pkg-03` — one package from each of
   `standards-wall` (both of them, since that's the two-package floor-risk
   category), one `clear-accept`, and one `silent-drift`, before paying for a
   full grade: `agreement: 4/4 scored items`, both `standards-wall` packages
   correctly rejected.
2. Full run, `--save-run`: `agreement: 18/20 scored items (bar: 18/20: PASS)`,
   `categories: clear-accept 5/7 not-tested 4/4 silent-drift 4/4
   standards-wall 2/2 unreviewable 3/3`.

That second score is the agreement line in the `eval-run.txt` I committed. I
didn't touch the rubric after this run — more on why in Trade-offs.

**Package analysis**

`pkg-01` — a pandas `read_csv` memory-overflow fix for a huge CSV. Gold label
`reject`, category `standards-wall`. My rubric said `reject` too.

This is the package this week's whole `standards-wall` category exists for.
The fix itself is good: it reads the chunking approach the maintainer actually
suggested in the thread, the diff is clean, and the before/after evidence
actually shows the memory drop. Nothing about the engineering is wrong. It
fails because the description never says which issue it closes and never adds
the changelog/whatsnew entry pandas's own template requires for every PR. My
`standards-met` check caught that specifically because it reads the repo-facts
template line and checks the description against it item by item, not against
how polished the PR looks. That's the whole point of splitting this into its
own check instead of folding it into `diff-is-reviewable` — a PR can be
completely reviewable and still miss something a reviewer would bounce it for
on sight.

**Check rationale**

The check, quoted exactly as it reads in the `rubric.md` I uploaded to
`tools/pr-precheck/`:

> | `description-claims-hold` | The claims the PR description makes about what the change does, read line by line against the diff and the plan. | Every claim the description makes is true of the diff in front of it. A description that says it implements the plan exactly must be describing a diff that does, and a description that names a file, a behavior, or an added entry must be describing one the diff contains. What fails is a claim the diff contradicts, which is worse than no claim at all because a reviewer stops looking once they have read it. | required |

I didn't revise this one after writing it — but it's the check that actually
did something useful on my own PR, not just on the eval packages, so it earns
its place here more than one I tweaked.

When I ran the skill live on my own draft before opening it for real, it came
back `reject` on exactly this check: my `pr_draft.md` had the box "CI is green
on this PR (all five jobs)" checked, but `test_evidence.md` only showed four
local commands — I'd never run the frontend suite, and the PR wasn't even open
yet when I wrote the draft, so there was no CI run to point to in the first
place. The rubric's own wording is what caught it: "a claim the diff
contradicts... a reviewer stops looking once they have read it." A checked box
with nothing behind it is exactly that — worse than leaving it unchecked,
because it tells a reviewer not to look. I went and actually ran
`cd frontend && npm test -- --run` (`18 passed`), added it to both files, and
re-ran the live check — it came back `accept`. The check worked exactly the
way it's written to: it didn't ding me for a smaller PR, it dinged me for a
claim with nothing under it, and the fix was to go get the evidence, not to
soften the wording.

**Trade-offs**

What this week's rubric gives up is `pkg-05` and `pkg-11`, both `clear-accept`
packages my rubric rejected on `evidence-is-observable` (and `pkg-11` also on
`standards-met`). I didn't chase these down with a reword, for the same reason
I learned on unit 3: `clear-accept` isn't a floor-risk category here — it's
5/7, nowhere near the one-package floor — and I was already over the 18/20
bar with every single-digit category (`standards-wall`, at 2/2) fully matched.
Loosening `evidence-is-observable` to catch two more accepts risks exactly the
category that actually has no margin, for a check that's supposed to be strict
about "evidence first." I'd rather carry two known, explained misses in a
category with slack than trade away the one check holding the floor category
together.

The cost that's left sits where I'd rather have it, same as last week: a
reviewer-facing precheck that's slightly too eager to reject a genuinely good
PR for thin evidence is a false alarm I can look at and overrule myself. A
precheck that's too eager to accept a PR nobody actually verified is the kind
of mistake that reaches a real reviewer's queue.

---

Related paths: `eval-run.txt` in this directory; my skill's files in
`tools/pr-precheck/`.
