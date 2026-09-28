# Rubric: is this reproduction package ready to post?

Every check below names a part of the package to read and a condition about
the outcome that someone else could apply and get the same answer. No check
grades the shape of the write-up: a terse report that shows the behavior
passes, and a long confident one that shows nothing fails.
`references/evidence-guide.md` is the map for where each kind of proof lives.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `environment-recorded` | The repro report's environment record, read against the version and platform the issue targets. | The report names the specific build of the software under test and the platform it ran on, concretely enough that a reader could assemble the same setup. Where the issue is specific to a platform or version, the record covers that dimension. A record that differs from the issue's target still passes if the report says so. Absent, or only generic wording such as "latest version" or "my machine", fails. | required |
| `steps-followable` | The repro report's steps, read as a stranger with nothing but a clean install and the issue page. | Every input the steps depend on is obtainable by the reader: commands appear with their arguments, and any file or config the run needs is either quoted or described precisely enough that a reader could construct an equivalent one themselves. What fails is an input the reader cannot get at all, such as a private repository, an internal configuration, or a fixture that exists only on the author's disk, and so does a sequence that omits a step the artifacts prove was taken. A described-but-constructible input passes; the test is whether the reader can obtain it, not whether it was pasted. | required |
| `behavior-matches-issue` | The report's artifacts, output excerpts, logs, or described observations, read line by line against the specific symptom the issue names, together with what the report says those artifacts are. | The report reads its own artifacts correctly against the issue. Two ways to pass: an artifact shows the same symptom the issue describes, the same error or crash, the same wrong value, the same missing or extra output; or the report states that it did not get the reported symptom and shows what it got instead. What fails is a mismatch the report does not notice: presenting an artifact as the issue's symptom when it is a different failure mode, such as a setup error, a validation message where the issue reports a crash, the right symptom from an input the issue does not name, or a related-but-distinct bug. A correctly labelled non-reproduction is not a mismatch; it is a result. | required |
| `outcome-stated-honestly` | The report's central conclusion, the sentence saying whether the behavior reproduced or did not, held against the artifacts in the same report that are supposed to back it. | The central conclusion is backed by an artifact in the report and matches what that artifact shows. A report that says it could not reproduce, and shows what it ran and what it got instead, passes: that is an honest outcome. What fails is overclaiming the conclusion: asserting a confirmed reproduction with no artifact at all, or with one that shows something else, or leaning on intensifiers and repetition counts where the artifact should be. Subsidiary observations along the way may be described in prose without their own output block; this check grades the conclusion the package rests on, not every sentence in it. | required |
| `claim-promises-only-investigation` | The candidate claim comment. | The claim names the specific behavior from this issue that the author is taking on, and states what the author will do next. It does not promise a fix, a delivery date, a guarantee, or that the issue be reserved or assigned for them, and does not assert a conclusion the package has not evidenced. | required |
| `conventions-and-disclosure` | The repo-facts block's stated contribution policy, including any AI-use or disclosure policy, read against the text of both candidate comments; and the stated bug-report template asks, read for substance rather than form. | Where the policy requires disclosing AI assistance, at least one comment discloses it and names the tool and the extent of the help; where the policy states no such requirement, silence about AI passes. The template's asks are satisfied when the comments carry the substance those fields exist to capture, typically the version and platform, whether or not they take the template's literal shape. A template ask is not failed merely because a field was answered in prose instead of pasted command output, nor because a template written for someone opening a new bug report was not filled in by someone adding a reproduction to an existing one. What fails is a stated disclosure requirement met with nothing. | required |
| `control-or-contrast` | The report's artifacts, looking for a second run that differs in one variable: a working case, an unaffected version, or the conditional path the issue contrasts. | The report includes a run that isolates the trigger, so a reader can see the behavior appear and not appear. | preferred |
| `next-step-is-named` | The candidate claim comment and the end of the repro report. | The author names the specific code path, file, or question they intend to look at next, rather than only that they will continue. | preferred |

## Verdict rule

Accept, meaning ready to post, if and only if every `required` check grades
`pass`. A single required `fail` holds the package.

`preferred` checks never change a verdict. They distinguish a package that is
merely ready from one that is genuinely useful to a maintainer.

`unclear` counts as `fail`, with one exception the skill already defines: on a
claim-only draft, the checks whose evidence is the repro report are reported
`unclear` with `not yet applicable: claim-only draft` and are left out of the
verdict rule entirely. Outside that case, proof that cannot be verified is
proof that is not ready to post.

## Why honesty and target are separate checks

They fail together often enough to look like one check, but they are not. A
report can show exactly the right symptom and then overstate what it proves,
and a report can be scrupulously honest about an artifact that happens to show
the wrong thing. Collapsing them would leave the verdict correct while the
`note` column named the wrong cause, and the note is what a student reads when
deciding which check to move.
