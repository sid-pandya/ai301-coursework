# Rubric: is this plan ready to post and build from?

Every check names a part of the package to read and a condition about the plan
itself that someone else could apply and get the same answer. No check grades
length or formatting: a terse plan that names its cause, its bounds and its
test can be ready, and a long confident one can be unbuildable.
`references/evidence-guide.md` says where each kind of evidence lives;
`procedure.md` says when and how to gather it.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `diagnosis-grounded` | The plan's stated cause, read against the repro-evidence block's artifacts and controls, and against any cause the issue or thread already establishes. | The stated cause explains the behavior the repro evidence actually shows, and is consistent with what the controls isolate. What fails is a cause the evidence contradicts, a cause that ignores what a control already ruled in or out, or dismissing part of the evidence as irrelevant without saying what else accounts for it. | required |
| `scope-bounded` | The plan's in-scope and not-in-scope statements, and the list of changes or steps it proposes, read against the one behavior the issue reports. | The plan proposes the change needed to address this issue and says what it is leaving alone. What fails is work the issue did not ask for riding along: dependency or version upgrades, refactors or new abstractions spanning other code paths, or reworking a subsystem because the author judges it unsound. Fixing the reported behavior in more than one file is not creep; changing things the issue never mentioned is. | required |
| `executable` | The plan's named files or areas and its approach or ordered steps. | A reader who has not spoken to the author could start work: the plan names the files or code areas it will change and what it will do in them. What fails is a plan whose first step is to discover what to do, such as profiling to find out what is slow, investigating an angle, or optimising whatever turns up, with no change named. Naming a mechanism the author will implement passes; naming an intention to find one does not. | required |
| `test-plan-decisive` | The plan's test plan, read against the repro evidence's steps and artifacts. | The test plan re-runs the reproduction, or an equivalent that exercises the same path, and says what will be observed instead of the reported symptom. What fails is a success condition nobody could check the same way twice, such as the behavior feeling faster or output looking better, with no command, no case, and no stated expected result. | required |
| `thread-aligned` | The thread-highlights section (live: the issue thread), specifically any comment from an OWNER, MEMBER, COLLABORATOR or CONTRIBUTOR that names a cause, proposes or rejects an approach, or reports a fix already in progress; read against the plan's diagnosis, scope and approach. | Where the thread carries maintainer direction, the plan engages with it: adopting it, or departing from it and saying why. What fails is a plan that contradicts a stated maintainer position without acknowledgement, proposes work a maintainer already rejected, or ignores a fix a maintainer has in flight and plans around it as if the thread were empty. Where the thread carries no maintainer direction, this check passes; and a plan may not claim thread support the thread does not contain. | required |
| `conventions-and-disclosure` | The repo-facts block's contribution policy, including any AI-use or disclosure policy and any contributor flow it states, read against the text of the candidate plan comment. | Where the policy requires disclosing AI assistance, the plan comment discloses it and names the tool and the extent of the help. Where the policy states no such requirement, silence about AI passes. Other stated contributor asks are satisfied when the comment carries the substance they exist to capture rather than their literal form. What fails is a stated requirement met with nothing. | required |
| `risks-stated` | The plan's risk, unknown, open-question or deviation statements. | The plan names at least one thing it is not certain of, or one cost it is accepting, in terms specific enough that a reviewer could respond to it. | preferred |
| `repro-quoted` | The plan's references to the reproduction. | The plan quotes or names the specific artifact, control or test case it relies on, rather than referring to the reproduction in general. | preferred |

## Verdict rule

Accept, meaning ready to post and build from, if and only if every `required`
check grades `pass`. A single required `fail` holds the plan.

`preferred` checks never change a verdict. They separate a plan that is ready
from one a maintainer will enjoy receiving.

`unclear` counts as `fail`. A plan whose readiness cannot be established from
the package is a plan that is not ready to build from: the package is what a
maintainer will read, and evidence that is not in it does not exist for this
purpose.

## Why thread alignment and conventions are separate checks

Both read the words around the plan rather than the plan's engineering, and it
is tempting to collapse them into one communications check. They catch
different mistakes. A plan can honour every stated policy and still talk past a
maintainer who has already posted the cause; a plan can engage perfectly with
the thread and still skip a disclosure the repo requires of everyone. Collapsing
them would still reject both, but the `note` column would name a single vague
cause, and that column is what a reader uses to decide which check to move.
