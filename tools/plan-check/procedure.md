# Procedure: how this skill grades a plan package

These are the operating steps. Follow them in order. Where a step says record
something, hold it for the checks that use it; the checks are defined in
`rubric.md` and the locations in `references/evidence-guide.md`.

## Read order

Read the whole package once in this order before grading any check. The order
matters because three checks compare the plan against something that must
already be in hand, and a reader who meets the plan first will tend to accept
the plan's own framing of the evidence.

1. Read the `## Issue` section. Record: the one behavior reported, and any cause
   the issue itself already names, with its file and line if given.
2. Read the `## Thread highlights` section. Record, for each comment, the
   author's association and whether that comment names a cause, proposes an
   approach, rejects an approach, or reports a fix already in progress. If
   there are no comments, record "no maintainer direction" explicitly, because
   a later step needs to tell an empty thread apart from one being ignored.
3. Read the `## Repo facts` block. Record the stated contribution policy, and
   separately whether it requires disclosing AI assistance.
4. Read the `## Repro evidence` block. Record the artifact, what each control
   run isolates, and the Expected and Actual lines. This is the behavior any
   diagnosis must explain.
5. Only now read the `## Candidate plan`, then the
   `## Candidate plan comment`.
6. Do not re-read the plan's framing of the evidence in place of the evidence.
   Where the plan describes what the repro showed, check that description
   against what step 4 recorded.

In live mode the same order applies, with `plan.md` and the draft comment as
the last two reads, and with the issue, thread, repo policy and the student's
posted repro comment gathered first from the locations in the evidence guide.

## Evidence gathering

For each evidence family a check names, take the fact from here and record it.

1. **Diagnosis and grounding.** Take the plan's cause statement verbatim. Set
   it beside the Expected/Actual lines and each control from read-order step 4.
   Record whether the cause explains the artifact, and whether it is consistent
   with what each control isolates.
2. **Scope.** Take the plan's in-scope statement, its not-in-scope statement if
   present, and its list of proposed changes. For each proposed change, record
   whether the issue's reported behavior requires it. Count the changes the
   issue never mentions.
3. **Executability.** Take the named files or code areas and the approach
   steps. For each step, record whether it names a change to make or an
   investigation to carry out.
4. **Test plan.** Take the test-plan text. Record the command or case it will
   run, and the observation it says will replace the symptom. Record whether
   that command or case corresponds to the repro evidence's steps.
5. **Thread alignment.** Use the per-comment record from read-order step 2.
   Record every maintainer position found, then record what the plan does with
   each one: adopts, departs with a stated reason, contradicts silently, or
   does not mention. Separately, record any claim in the plan that the thread
   says something, and check it against the actual comments.
6. **Conventions and disclosure.** Use the policy record from read-order step
   3. If AI disclosure is required, search the plan comment's text for a
   disclosure and record whether it names a tool and an extent. If not
   required, record "no disclosure requirement".
7. **Risks and repro quoting.** Record any risk, unknown or open question the
   plan states, and record whether the plan names specific repro artifacts or
   refers to the reproduction only in general.

Gather every family before grading, so no check is graded from a half-read
package. If a family's evidence is genuinely absent from the package, record it
as absent with the section you looked in; absent is a finding, not a reason to
go looking outside the package.

## Check execution

1. Execute the checks in this order: `diagnosis-grounded`, `scope-bounded`,
   `executable`, `test-plan-decisive`, `thread-aligned`,
   `conventions-and-disclosure`, then the preferred checks `risks-stated` and
   `repro-quoted`. This order runs the engineering checks before the
   communications checks so that a plan is never failed on its wording before
   its substance has been read.
2. Execute every check even after one has failed. The verdict needs only one
   required failure, but the output is also feedback, so a complete grade is
   more useful than an early exit.
3. Grade each check only from the facts recorded in Evidence gathering. Do not
   re-read the package for a check whose family was already gathered; if a
   check cannot be graded from the record, that means a gathering step was
   skipped, so return to it rather than improvising.
4. Grade `pass` when the pass condition in `rubric.md` is met, `fail` when it
   is not, and `unclear` only when the evidence the check names is genuinely
   absent from the package. Not having looked is never `unclear`.
5. For every grade, write one line of evidence: a quote from the package, or a
   recorded fact, that decided it. A grade with no named fact is not a grade.
6. Where `rubric.md` and this procedure disagree about what a check decides,
   `rubric.md` wins; this file decides only how the work is done. Where this
   procedure is silent on a step, say so in the summary rather than inventing
   one.

## Verdict assembly

1. Collect the grades for the `required` checks only:
   `diagnosis-grounded`, `scope-bounded`, `executable`,
   `test-plan-decisive`, `thread-aligned`, `conventions-and-disclosure`.
2. Convert each `unclear` on a required check to a failure, per the rubric's
   verdict rule.
3. If every required check is `pass`, the verdict is `accept`. If one or more
   is `fail`, the verdict is `reject`. There is no third verdict and no
   weighing of how many failed.
4. Do not let `preferred` grades move the verdict in either direction. Report
   them, and on an accepted plan mention them as what makes it better than
   merely ready.
5. Name the deciding check. On a reject, that is the first required check that
   failed in execution order, and its one-line evidence is quoted in the
   summary. On an accept, say that all required checks passed.
6. Emit the readable summary first, one line per check with its grade and
   evidence, plus any voice-guide note in live mode, then the fenced JSON block
   required by `SKILL.md` as the last thing in the output.
