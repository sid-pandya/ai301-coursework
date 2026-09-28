# Evidence guide: where proof lives in a reproduction package

The map for the checks in `rubric.md`. For each family: where to look, and
what good looks like when you get there.

## Environment

**Where it lives.** In an eval bundle: the opening lines of the
`## Candidate repro report` section, usually a single line beginning
"Environment:". Cross-read it against the `## Issue` section, which states the
version the reporter was on, and against the `## Repo facts` block, whose
`latest release` line says what current is. In live mode: the environment
paragraph of the draft report, checked against the version named in the issue
body and against the repo's bug-report template, which usually lists the
version and platform fields it wants.

**What good looks like.** The record names the build of the software under test
(a release number, a tag, or a commit), how it was installed, and the operating
system and architecture it ran on. Where the issue is bounded by a platform or
a dependency, the record covers that dimension: a Windows-only bug needs the
Windows version, a dependency regression needs that dependency's version. A
record that names a different version than the issue targets is still good if
the report says which and why. "Latest version", "my machine", or an
environment line that is simply absent is not a record.

## Steps

**Where it lives.** In an eval bundle: the numbered or fenced sequence under
the report's "Steps" heading, plus any config file contents quoted alongside
it. In live mode: the same part of the draft, read against the reproduction the
issue itself supplies, so you can see whether the author followed it or
substituted their own path.

**What good looks like.** A stranger with a clean install and the issue page
can get from their starting state to the moment the behavior appears without
asking a question. Concretely: commands appear with their arguments and flags;
files the run depends on are quoted, not described; the state the run begins
from is reachable, meaning a fresh install, a public repository at a named
commit, or a fixture given in the report. The steps must also account for
everything the artifacts show happened. If a log excerpt mentions a service, a
file, or a flag that no step creates, the sequence is incomplete even though
each listed step is clear. The failure to watch for is a step that rests on
something unobtainable: a private monorepo, an internal configuration that
cannot be shared, a fixture that exists only on the author's disk. That report
may be entirely true and still be unfollowable, which is what this family
grades.

## Behavior shown

**Where it lives.** In an eval bundle: the fenced output blocks, log excerpts,
and described observations inside the report. Read each against the
`## Issue` section's own output blocks and its statement of the symptom, and
against `## Thread highlights`, where a maintainer has sometimes already named
what the correct behavior would be. In live mode: the same artifacts in the
draft, read against the issue body on GitHub.

**What good looks like.** The artifact shows the symptom the issue named, in
the same terms: the same panic or exception, the same wrong value, the same
header or line that should be present and is not. The test is substitution — if
you covered the report's prose and showed a maintainer only the artifact, would
they recognise their own bug?

An artifact shows an **adjacent** behavior when it is a real failure in the
same feature but not the reported one. The cases to watch: a validation or
usage error where the issue reports a crash; a crash with a different message
or at a different call site; a setup or installation failure that stopped the
run before the behavior could occur; the right symptom triggered by a different
input than the issue names. Each of these is a genuine observation and none of
them is a reproduction. An adjacent artifact is more dangerous than an absent
one, because it reads as proof.

## Honesty

**Where it lives.** Where the report's assertions meet its artifacts: the
"Expected" and "Actual" lines, the summary sentence at the top or bottom, and
any sentence claiming the bug is confirmed. In live mode, the same sentences in
the draft.

**What good looks like.** Each assertion can be traced to an artifact in the
same report that shows it. The direction that matters is overclaiming: prose
that asserts a confirmed crash while the pasted output shows a clean error
exit, or "reproduces consistently" with one run shown, or intensifiers
("careful and thorough investigation", "ran it ten times") standing where an
artifact should be. Confidence is not evidence, and emphasis is often what a
report reaches for when it has none.

A report that says it **could not** reproduce is a pass for this family when it
shows its work: the environment it tried, the steps it ran, and the output it
got instead of the reported symptom. That is a genuine result and useful to a
maintainer. What fails is not the negative outcome but an unevidenced one in
either direction: "can confirm this happens for me too" with nothing attached
is the same failure as a false confirmation.

## Comms

**Where it lives.** The `## Candidate claim comment` section and the report's
framing sentences, read against the `## Repo facts` block, specifically its
`bug reports` line, which states what the repo's template asks for, and its
`contribution policy` line, which states any AI-use rule. In live mode: the
draft comments against `CONTRIBUTING.md`, the issue and PR templates in
`.github/`, and any `AI_POLICY.md` or equivalent, which often sits one click
away from the contributing guide.

**What good looks like.** The claim names this issue's specific behavior rather
than any issue's, says what the author is about to do, and stops there. It
promises investigation and a report back; it does not promise a fix, a date, a
guarantee, or ask that the issue be reserved or assigned. It states the
author's position plainly without performing either expertise or deference.

On policy: **disclosure requirements are conditions, and conditions are met, not
argued with.** Where the stated policy requires disclosing AI assistance, a
comment satisfies it by naming the tool and the extent of the help in the
comment itself, where a reader of the thread will see it. A policy that says
nothing about AI requires no disclosure, and silence there is not a violation.
The failure this family exists to catch is the quiet one: a package whose proof
is excellent and whose comments simply do not do a thing the repo asked every
contributor to do. Nothing about the reproduction reveals it. Only the policy
line does.
