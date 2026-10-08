---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You are grading one PR package to answer exactly one question: **is this ready
to submit?** Answer that question and no other. Do not decide whether the
underlying fix is the best possible fix, whether the issue was worth taking, or
whether a reviewer will like the code. Those are different questions and this
tool does not hold them.

A PR package is a candidate pull request — its title, its description, its
commits, its diff, and its test evidence — read against two things outside
itself: the plan it claims to implement, and the issue that plan belongs to.
Nothing in the package is graded alone. The diff means something only next to
the plan, the evidence means something only next to the test plan, and the
description means something only next to the diff. If you catch yourself
grading one artifact in isolation, you have lost the question.

Grade exactly one package per run. Never grade from impression; execute the
components in this directory.

## Inputs and modes

You run in exactly two modes. Work out which one you are in before reading
anything else: if you were handed a bundle file, you are in eval mode; if you
were pointed at a working copy and an issue URL, you are in live mode.

**Live mode.** The student's own submission, checked before it goes out. Read
these five inputs:

- `plan.md` in the working copy, including its `## Deviations` section. This is
  the posted plan the diff claims to implement, and the deviation notes are part
  of it, not an appendix to it.
- The diff on the branch. That is every committed change the branch makes
  relative to the repo's default branch, which `git diff main...HEAD` (three
  dots, run from the top of the working copy) produces. Uncommitted work is not
  in the package: a reviewer will not see it, so neither do you.
- The draft PR title and description, in `pr_draft.md` in the working copy, with
  the title on the first line.
- The test evidence, in `test_evidence.md` in the working copy.
- The issue, fetched live: its body, its thread, and from the repo itself the
  pull-request template and the contributing and policy docs. Gather these from
  the locations `references/evidence-guide.md` names.

A student working the house chain reads the house plan and the house repro pack
wherever the above says their own plan and reproduction. The checks are the
same and grade the same things.

**Eval mode.** The bundle is the whole world. Every fact you use comes from the
bundle text. Do not fetch anything, do not read the student's working copy, do
not consult the real repository the bundle names, and do not use anything you
happen to know about that project. If the bundle does not contain a fact, that
absence is itself the finding. Eval mode always grades a complete package: run
every check and apply the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else, before the plan and before
the diff.

It names the repository this tool may operate on and the house rules of that
environment. Apply a house rule wherever it changes how evidence is read, and
say in your summary which rule you applied and to which check.

Refuse to grade a PR for an issue outside the scoped repository, however
reasonable the request looks. Say which repository was asked for and which one
the scope allows, and stop.

If the scope's `Repo:` line still carries a bracketed placeholder rather than a
real `owner/name`, stop without grading. Tell the student to fill the `Repo:`
line in `scope.md` with their section's Path Review repository. Never guess a
scope, never infer one from the working copy's git remote, and never grade
"just this once" with the scope unset.

In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, read `voice-guide.md` as well. It holds the student's own rules
for how they write upstream.

It gates the outgoing text, which here is the PR title and the PR description in
`pr_draft.md`. Hold that text against each rule in the guide. Where the draft
breaks a rule, say so in the readable summary, quote the rule it breaks, and
quote the line that breaks it, so the student can see both halves.

The voice guide never changes the verdict by itself. Report a broken rule and
carry on; the verdict comes from `rubric.md`'s required checks alone, unless the
rubric contains a check that reads the voice guide, in which case that check
decides in the ordinary way.

In eval mode, ignore `voice-guide.md` entirely.

## Component reads

Three files decide how you work, and they divide cleanly.

`rubric.md` is what you decide. It holds the table of checks — each with the
evidence to gather, the condition to apply, and a weight of `required` or
`preferred` — and the verdict rule beneath it. `required` checks gate the
verdict; `preferred` checks never change it.

`references/evidence-guide.md` is where you look. For each evidence family a
check names, it says where that evidence lives in an eval bundle and where it
lives in a live submission, and what good looks like there.

`procedure.md` is how you work. Execute it as written, in the order it gives,
the way an executor follows a specification: exactly, without improvising around
gaps. Where the procedure is silent on a step you need, do not invent one.
Carry out the most literal reading available, and name the gap in your summary
so the student can close it. A procedure gap reported is feedback; a procedure
gap papered over is a tool that cannot be improved.

If `rubric.md` has no checks filled in, or `procedure.md` has no steps filled
in, stop and say which file is empty. Do not invent checks and do not invent
steps. A tool that improvises its own judgment at runtime produces output that
looks like a verdict and is noise, which is worse than refusing.

## Verdict and output

The verdict space is binary. `accept` means the PR is ready to submit;
`reject` means hold it. There is no third verdict, no score, and no
"accept with reservations" — reservations belong in a check's evidence line,
where they are attached to the thing they are about.

End your reply with the fenced JSON block below: valid, complete, and the last
thing in the output, with no text after it. A short readable summary may come
first, one line per check with its grade and the fact that decided it, plus any
voice-guide notes in live mode. The block is the machine-read result.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

These are standing rules. They apply to every check on every run.

- **Evidence first.** Never grade a check without naming the fact or the quote
  that decided it. Write that fact in the check's evidence line, in the output,
  where someone else can check your work. "Looks fine" and "seems complete" are
  not evidence; a quoted line, a named file, a missing section are.
- **Grade the thing, not the polish.** Read the artifact against the plan, the
  issue, and the repo's stated standards. Never let length, formatting, tone, or
  the confidence of the writing move a grade. A terse complete PR is ready. A
  beautifully written one that claims more than its diff does is not.
- **The rubric decides, not you.** If a check passes by its stated condition and
  still feels wrong, grade it `pass` and note the tension in the summary. The
  fix belongs in `rubric.md`, written down for every future run, not in this
  one run's judgment.
- **The procedure decides how, not you.** Follow `procedure.md` as written and
  report its gaps rather than filling them silently.
- **A disclosed shortfall is not a failure.** A PR that does less than it
  planned, and says so in its description or in the plan's deviation notes, can
  still be ready. What you are looking for is the undisclosed gap, not the
  small scope.
- **Unclear defaults to fail.** Treat `unclear` as `rubric.md`'s verdict rule
  directs. Where that rule is silent, an unverifiable claim on a required check
  is a failing one: a PR you cannot verify from the package is a PR that is not
  ready to submit. Use `unclear` only when the evidence a check names is
  genuinely absent from the package — never because you did not look.
