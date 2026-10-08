# Evidence guide: where evidence lives in a PR package

The map for the checks in `rubric.md`. For each family: where it lives in an
eval bundle, where it lives in a live submission, and what good looks like when
you get there. `procedure.md` says when each family gets gathered; this file
says where it is.

## Plan fidelity (harness category: silent-drift)

**Where it lives.** In an eval bundle, three sections read together: the
`## Plan context` section, which carries the accepted plan's scope statement,
its named files, its approach and any deviation note; the `### Diff` block under
`## Candidate PR`, which is what actually changed; and the `### Description`
block, for what the PR says about itself. In live mode: `plan.md` in the working
copy including its `## Deviations` section, the output of
`git diff main...HEAD` run from the top of the working copy, and the description
in `pr_draft.md`.

**What good looks like.** Walk the diff hunk by hunk, not file by file, and ask
of each hunk: did the plan name this? A matching diff is one where every hunk is
work the plan called for, and everything the plan called for is either present
or openly missing. An honest deviation re-ties a mismatch completely: a plan
whose `## Deviations` section says the fix needed one more file, and why, is a
plan that now covers that file, and the diff matches it again. Disclosure is
what makes a mismatch disappear, not size.

Silent drift runs in two directions and both fail. **More than the plan** is the
common one, and it hides well because the extra work usually looks like an
improvement: a function rewritten while the author was in the file, a rename
tidied up, an unrelated bug fixed in passing. The test is not whether the change
is good but whether anyone was told. **Less than the plan**, with no note, is
the quieter one: a plan promising a fix plus a regression test, and a diff with
no test and a description that does not mention its absence.

The description's fidelity claims belong to this family, not to comms. A
description saying it "implements the posted plan exactly" next to a diff that
also rewrites a helper is silent drift, and it is worse than the bare extra hunk
would have been, because the sentence tells a reviewer they need not check.

## Test evidence (harness category: not-tested)

**Where it lives.** In an eval bundle, the `### Test evidence` block under
`## Candidate PR`, read against the test plan quoted in `## Plan context` and
against the reproduction the issue and plan rest on. In live mode:
`test_evidence.md` in the working copy, read against `plan.md`'s test plan and
the student's posted repro comment on the issue, plus whatever the repo's own
documents say its checks are.

**What good looks like.** Someone who was not there can see what happened. That
means the command appears with its output, or a named test appears with its
result, for the behavior **before** the change and **after** it, so the
difference is visible rather than asserted. The strongest form re-runs the
reproduction the issue was filed on, shows the symptom in the before and its
absence in the after, and adds the repo's own checks with their results. Controls
re-run unchanged are a bonus: they show nothing else moved.

The failure to recognise is assertion standing where observation should be.
"Tested locally and it works now", "colors display correctly", "`cargo test`
passes" — each may be perfectly true, and none of them is evidence, because
nothing in them is anything a reader could have watched happen. Confidence is
not proof, and a sentence about a test suite is not the test suite's output.

A reported failure is still evidence and still passes this family. A check that
errored, a suite with a known-failing test, a step the author could not run —
reported plainly, with the reason — tells a reviewer more than silence does.
What fails is the gap, not the bad news.

## Diff quality (harness category: unreviewable)

**Where it lives.** The `### Diff` block and the `### Commits` list in an eval
bundle; `git diff main...HEAD` and `git log main..HEAD` in live mode.

**What good looks like.** A reviewer reads the diff once and sees only the
change. Every line is either the fix, its test, or a documentation or changelog
entry the repo asks for. Nothing has to be mentally deleted on the way past.

The debris tells, in rough order of how often they appear: a commented-out line
or block kept in case it is wanted later; a debug print, log line or
`eprintln!`/`console.log` added during the work and never removed; a `TODO` or
`FIXME` added in passing on a line the fix happened to touch; whitespace or
import reordering across lines the change does not otherwise touch; and a hunk
in a file that has nothing to do with the issue.

Judge debris by presence, not by proportion. One commented-out debug line in a
six-line diff is the whole problem, because it tells a reviewer the author did
not read their own change before sending it, and that is exactly the doubt a
pre-check exists to remove. Note that debris differs from drift: drift is work
the plan did not name, debris is content that is not work at all.

## Standards and comms (harness category: standards-wall)

**Where it lives.** In an eval bundle, the `## Repo facts` block, specifically
its `pull requests` line, which states what the repo's PR template requires, and
its `contribution policy` line, which states any contributing or AI-use rule;
read against the `### Title` and `### Description` blocks. The
`## Thread highlights` section carries any explicit maintainer direction. In
live mode: `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` in the
repo, any `AI_POLICY.md` or equivalent, and the live issue thread, read against
`pr_draft.md`.

**What good looks like.** The description answers each thing the repo actually
asks a PR for. Read the template's asks as a list and tick them off against the
description's substance: an issue reference where the template asks for one, a
changelog or release-notes entry where it asks for one, a type-of-change
statement where it asks for one, a test confirmation where it asks for one. The
asks are met by substance, not by shape — a description that answers everything
in its own prose satisfies this family even if it never reproduces the
template's headings, and one that reproduces every heading with nothing under
them does not.

**Disclosure requirements are conditions, and conditions are met, not argued
with.** Where the stated policy requires disclosing AI assistance, the
description discloses it, names the tool, and says what the help consisted of,
in the description itself where a reviewer will see it. A repo silent on AI
requires no disclosure, and silence there is not a violation.

This is the quiet category, and the reason it needs its own checks. A package
can be excellent on every other family — faithful diff, clean hunks, decisive
before-and-after evidence — and still fail here, because nothing about the
engineering reveals a missing changelog entry or an unreferenced issue number.
Only the repo-facts line reveals it, so the only way to catch it is to go and
read what the repo asked for and compare, every time.
