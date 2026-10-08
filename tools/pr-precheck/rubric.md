# Rubric: is this pull request ready to submit?

Every check names a part of the package to read against another part, and a
condition about the outcome someone else could apply and get the same answer.
No check grades shape: a three-line description can be ready and a long polished
one can be hiding drift. `references/evidence-guide.md` says where each family
lives; `procedure.md` says when and how to gather it.

One rule runs through all of them. **A disclosed shortfall is not a failure.** A
PR that does less than its plan, or leaves something out, and says so in its
description or in the plan's deviation notes, is still ready. What these checks
exist to catch is the undisclosed gap: the change nobody mentioned, the claim
nothing backs, the repo ask nobody answered.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `diff-matches-plan` | Every changed hunk in the diff, read against the plan context's scope statement, named files and approach, together with any deviation note the plan carries. | Each hunk is work the plan named, or work a deviation note records. Work the plan named that is missing from the diff passes too, as long as the description or a deviation note says it is missing. What fails is an undisclosed gap in either direction: a hunk touching something the plan never mentioned and nothing discloses, or planned work silently absent. A refactor, rename, or cleanup riding along with the fix is the common case, and it fails whether or not it improves the code. | required |
| `description-claims-hold` | The claims the PR description makes about what the change does, read line by line against the diff and the plan. | Every claim the description makes is true of the diff in front of it. A description that says it implements the plan exactly must be describing a diff that does, and a description that names a file, a behavior, or an added entry must be describing one the diff contains. What fails is a claim the diff contradicts, which is worse than no claim at all because a reviewer stops looking once they have read it. | required |
| `evidence-is-observable` | The test-evidence section, read against the plan's test plan and against the reproduction the issue and plan rest on. | The evidence shows what was run and what came back: commands with their output, or a named test with its result, for the behavior before the change and after it, plus the result of the repo's own checks where the repo states them. An honestly reported failing or skipped check still passes this, with its reason. What fails is assertion standing where observation should be: that it was tested locally, that it works now, that the suite passes, with nothing a reader could have watched happen. | required |
| `diff-is-reviewable` | The diff's hunks, read for content that is not the change: commented-out code, debug or logging statements added by this change, stray TODO or FIXME notes, reformatting of untouched lines, and hunks in files unrelated to the fix. | The diff contains the change and nothing a reviewer has to mentally delete. What fails is debris left in: a commented-out line kept for later, a debug print, a TODO added in passing, or a whitespace-only reflow mixed into the same hunks. Debris is judged by presence, not by size; one commented-out line in a six-line diff is the whole problem. | required |
| `standards-met` | The repo-facts block's stated pull-request template asks and its contribution or AI policy, read against the PR title and the description text. | The description supplies each thing the repo's stated template requires of a PR: an issue reference where the template asks for one, a changelog or release-notes entry where the template asks for one, a type-of-change statement where the template asks for one. Where the stated policy requires disclosing AI assistance, the description discloses it and names the tool and the extent. An ask is met by substance, not by matching the template's literal headings. What fails is a stated requirement answered with nothing. | required |
| `title-is-specific` | The PR title. | The title names the behavior being fixed or changed, specifically enough that a reviewer scanning a list could tell what this PR does without opening it. | preferred |
| `commits-are-scoped` | The commit list, read against the diff. | The commits separate the change from its test or docs, or are a single commit for a single-shape change, rather than one commit mixing unrelated work. | preferred |

## Verdict rule

Accept, meaning ready to submit, if and only if every `required` check grades
`pass`. A single required `fail` holds the PR.

`preferred` checks never change a verdict in either direction. They separate a
PR that is ready from one a reviewer will enjoy receiving, and on an accepted
package they are reported as such.

`unclear` counts as `fail` on a required check. The package is what a reviewer
will see; evidence that is not in it does not exist for this purpose, and a PR
whose readiness cannot be established from the package is not ready to submit.
On a `preferred` check, `unclear` is reported and changes nothing.

## Why drift is split into two checks

`diff-matches-plan` and `description-claims-hold` both catch silent drift, and a
package that drifts usually fails both. They are separate because the fix
differs. A diff that outgrew its plan is fixed by updating the plan's deviation
notes or dropping the extra hunk; a description that overstates a faithful diff
is fixed by rewriting one sentence. Collapsing them would still reject the
package, but the `note` column would not tell the author which of those two
things to do, and that column is the only feedback a failing run gives.
