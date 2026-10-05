# Evidence guide: where evidence lives in a plan package

The map for the checks in `rubric.md`. For each family: where to look, and what
good looks like when you get there. `procedure.md` says when each family gets
gathered; this file says where it is.

## Diagnosis and grounding

**Where it lives.** The plan's opening cause statement, usually a line or
paragraph beginning "Diagnosis" or under a `### Diagnosis` heading. Read it
against two other places: the `## Repro evidence` block, especially its
artifact, its control runs and its Expected/Actual lines, and the `## Issue`
section, which often already names a cause with a file and line. In live mode:
the diagnosis section of `plan.md`, read against the student's posted repro
comment on the issue and against the issue body.

**What good looks like.** The cause names a mechanism, and the mechanism
accounts for what the artifact shows. The controls are the sharpest test: if a
control isolates the trigger, a grounded diagnosis is consistent with that
isolation and usually says so. A diagnosis is not grounded when it names a
different mechanism than the evidence supports, when it waves away a dimension
the evidence establishes as a red herring without saying what then explains the
observation, or when it would predict the controls behaving differently than
they did.

## Scope

**Where it lives.** The plan's in-scope and not-in-scope statements, and its
numbered list of proposed changes. The not-in-scope line is often the most
informative sentence in a plan. Read the list against the `## Issue` section's
single reported behavior. In live mode: the scope section of `plan.md`.

**What good looks like.** The proposed work addresses the reported behavior and
stops. An explicit not-in-scope line that names a tempting adjacent change and
declines it is the strongest signal a plan is bounded. Creep looks like changes
the issue never mentions arriving alongside the fix: a dependency or runtime
version bump, a new abstraction unifying code paths that happen to be nearby, a
rewrite justified by the author's judgment that the surrounding design is
unsound, or a list of improvements where the reported bug is one item. Touching
several files to fix one behavior is not creep; the question is whether each
change is required by the behavior the issue reports.

## Executability

**Where it lives.** The plan's files or areas list and its approach or ordered
steps. In live mode: the same sections of `plan.md`.

**What good looks like.** The plan names where the change lands and what the
change is, concretely enough that someone else could open the file and begin.
"Clamp the fill with `saturating_sub` in `src/printer.rs`, and apply the same
clamp at the sibling advance" is executable. The failure to watch for is a plan
whose steps are investigation: profile and find the slow part, look into
whether a different install behaves differently, explore caching, optimise
whatever shows up. Those are reasonable things to do, but they describe how the
author will decide what to build, not what they will build, so nobody can start
and nobody can tell when it is done.

## Test plan

**Where it lives.** The plan's test-plan section, read against the
`## Repro evidence` block's steps and artifacts. In live mode: the test plan in
`plan.md` against the commands in the posted repro comment.

**What good looks like.** The test plan reuses the reproduction as its check:
the same command or test case, run against the change, with the expected
observation stated. Decisive means someone else could run it and agree on the
answer, so it names the command or case, and names what will be seen instead of
the symptom, often with the controls re-run unchanged to show nothing else
moved. A regression test added at the reproducing input is a strong form of
this. A vague test plan states a feeling or a direction: the prompt should feel
fast, timings should look better, the output should be correct. Those have no
pass line.

## Honesty

**Where it lives.** The plan's risk, unknown and open-question statements,
typically at the end, and after a build, its `## Deviations` section. In live
mode: the same sections of `plan.md`, plus any follow-up comment on the thread.

**What good looks like.** The plan names something it does not know, or a cost
it has decided to accept, specifically enough that a reviewer could answer it:
an unmeasured performance cost with the fallback the author would take, or a
question about whether a sibling code path has the same defect, parked for
follow-up. False confidence reads as a plan with no unknowns at all in an area
the evidence has not fully mapped. After a build, an honest deviation is
recorded in the plan with what changed and why; a deviation visible only in the
diff is not recorded at all.

## Comms

**Where it lives.** The `## Candidate plan comment` section, read against two
other places: the `## Thread highlights` section, for what maintainers have
already said, and the `## Repo facts` block's `bug reports` and
`contribution policy` lines, for what the repo asks of contributors, including
any `AI_POLICY.md` or disclosure requirement. In live mode: the draft comment
against the live issue thread, `CONTRIBUTING.md`, the `.github/` templates, and
any AI policy file, which often sits one click away from the contributing guide.

**What good looks like.** Thread-aware means the comment is written by someone
who read the thread. Where a maintainer has named a cause, proposed an approach,
rejected one, or posted a patch already, the comment shows it: it adopts the
direction, or departs from it and says why. The two failures are opposite and
both matter. One is talking past the thread: planning as though the thread were
empty when a maintainer has a fix in flight, or proposing an approach already
rejected on cost. The other is claiming thread support that is not there:
citing a direction "already given in the thread" that no comment contains.

On policy: **disclosure requirements are conditions, and conditions are met, not
argued with.** Where the stated policy requires disclosing AI assistance, the
comment satisfies it by naming the tool and the extent of the help in the
comment itself, where a reader of the thread will see it. A policy silent on AI
requires no disclosure. The quiet failure to watch for is a plan that is
excellent on every engineering dimension and simply does not do a thing the repo
asks of every contributor; nothing in the diagnosis, the scope or the test plan
reveals it, and only the policy line does.
