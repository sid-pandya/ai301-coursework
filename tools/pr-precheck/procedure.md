# Procedure: how this tool grades a PR package

These are the operating steps. Follow them in order. Where a step says record
something, hold it for the checks that use it; the checks are in `rubric.md`
and the locations are in `references/evidence-guide.md`.

Nearly every step here compares two things. That is the shape of the week: a
diff means nothing alone, and neither does a description. Gather both sides
before grading either.

## Read order

Read the whole package once, in this order, before grading any check. The order
exists because four checks compare the PR against something that must already be
in hand, and a reader who meets the PR's description first will adopt its
framing of its own diff.

1. Read the issue. Record the one behavior reported, and the issue number.
2. Read the thread, if the package has one. Record any comment from an OWNER,
   MEMBER, COLLABORATOR or CONTRIBUTOR that states a requirement or a direction.
   If there is none, record "no maintainer direction" explicitly, so a later
   step can tell an empty thread from an ignored one.
3. Read the repo facts. Record two lists separately: what the PR template
   requires of every PR, and what the contribution or AI policy requires,
   including whether AI disclosure is required.
4. Read the plan, including its deviation notes. Record the scope statement, the
   not-in-scope statement, the named files, and the test plan. Treat the
   deviation notes as part of the plan, not as commentary on it.
5. Read the diff. Record each hunk as one line: which file, and what it does.
   Do not yet judge any of them. A hunk list built before reading the
   description is the only version of the diff not shaped by the description.
6. Read the commits, the title, and the description, in that order.
7. Read the test evidence last. Record each command or test named and what
   output is shown for it.

Do not substitute the description's account of the diff for the diff. Where the
description says what changed, check it against the hunk list from step 5.

In live mode the same order applies, with the issue, thread, PR template and
policy gathered from the repo per the evidence guide, `plan.md` as step 4, the
output of `git diff main...HEAD` as step 5, `pr_draft.md` as step 6, and
`test_evidence.md` as step 7.

## Evidence gathering

For each family a check names, take the fact from here and record it.

1. **Plan fidelity.** Take the hunk list from read-order step 5 and the plan
   record from step 4. Walk the two lists against each other in both directions.
   For each hunk, record whether the plan named that work, a deviation note
   records it, or neither. For each thing the plan named, record whether the
   diff contains it, and if not, whether the description or a deviation note
   says it is missing. Then take each claim the description makes about what
   changed, and record whether the hunk list bears it out.
2. **Test evidence.** Take the evidence record from read-order step 7 and the
   plan's test plan from step 4. For each behavior the test plan named, record
   whether the evidence shows a before, an after, both, or neither, and whether
   what is shown is observable output or an assertion. Record separately the
   outcome of each repo check the repo facts said exists, including any reported
   as failing or skipped.
3. **Diff quality.** Walk the hunk list again, this time looking only for
   content that is not the change: commented-out code, debug or logging lines,
   TODO or FIXME notes added by this diff, formatting-only edits to lines the
   change does not otherwise touch, and hunks in files unrelated to the issue.
   Record each one found, with its file and line text. Record "none found" if
   there are none, so the check is graded from a search rather than an
   impression.
4. **Standards and comms.** Take the two requirement lists from read-order step
   3. For each item on the template list, record whether the description
   supplies it in substance and quote where. If AI disclosure is required,
   record whether the description discloses it and whether it names a tool and
   an extent. Take the maintainer-direction record from step 2 and record what
   the PR does with any requirement stated there.

Gather every family before grading anything, so no check is decided from a
half-read package. Where a family's evidence is genuinely absent, record it as
absent and name the section you looked in; absent is a finding, and in eval mode
it is never a reason to look outside the bundle.

## Check execution

1. Execute the checks in this order: `diff-matches-plan`,
   `description-claims-hold`, `evidence-is-observable`, `diff-is-reviewable`,
   `standards-met`, then the preferred checks `title-is-specific` and
   `commits-are-scoped`. Substance before presentation: a PR is never failed on
   its wording before its diff has been read against its plan.
2. Execute every check even after one has failed. The verdict needs one required
   failure, but the output is also the author's feedback, and a run that stops
   early tells them to fix one thing and resubmit into the next failure.
3. Grade each check only from the facts recorded during Evidence gathering. If a
   check cannot be graded from the record, a gathering step was skipped; go back
   and do it rather than re-reading the package ad hoc.
4. Before grading any check `fail` for something missing, check the disclosure
   escape hatch: if the description or the plan's deviation notes openly state
   the shortfall, the check passes. Record the quote that discloses it as the
   check's evidence. This rule applies to a smaller diff than planned, a step
   not run, and a check that failed and was reported.
5. Grade `pass` when the rubric's pass condition is met, `fail` when it is not,
   and `unclear` only when the evidence the check names is genuinely absent from
   the package. Not having looked is never `unclear`.
6. Write one line of evidence for every grade: a quoted line, a named file, or a
   recorded fact. A grade with no named fact is not a grade, and that line is
   the only thing the author can act on.
7. Where `rubric.md` and this procedure disagree about what a check decides,
   `rubric.md` wins; this file decides only how the work is done. Where this
   procedure is silent on a step, say so in the summary rather than inventing
   one.

## Verdict assembly

1. Collect the grades of the `required` checks only: `diff-matches-plan`,
   `description-claims-hold`, `evidence-is-observable`, `diff-is-reviewable`,
   and `standards-met`.
2. Convert each `unclear` on a required check to a failure, per the rubric's
   verdict rule.
3. If every required check is `pass`, the verdict is `accept`. If one or more is
   `fail`, the verdict is `reject`. There is no third verdict, no weighing of
   how many failed, and no partial credit for failing narrowly.
4. Do not let `preferred` grades move the verdict in either direction. Report
   them, and on an accepted package name them as what makes it better than
   merely ready.
5. Name the deciding check. On a reject, that is the first required check that
   failed in execution order; quote its evidence line in the summary so the
   author sees the one fact that held their PR. On an accept, say that every
   required check passed and name any preferred check that did not.
6. Emit the readable summary first — one line per check with its grade and its
   evidence, plus any voice-guide note in live mode and any procedure gap you
   hit — then the fenced JSON block required by `SKILL.md`, valid and last, with
   nothing after it.
