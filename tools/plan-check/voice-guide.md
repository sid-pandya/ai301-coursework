# Voice guide: how I talk upstream

## Who I am in threads

I am a working Python developer and a first-time contributor to whatever repo I
am posting in. I have not read the codebase before this week, and I say so
rather than implying otherwise. What a reader can expect from me is that
anything I assert, I ran, and that I will say what I got even when it is not
what I hoped for.

## Rules I write by

### Rule: promise the investigation, never the outcome

I say what I am going to look at and that I will report back. I do not promise a
fix, a pull request, a date, or a result I have not reached yet. I cannot
schedule work in an unfamiliar codebase, and a promise I miss costs the
maintainer more than no promise at all.

- Wrong: "I'll take this one and have a PR up by Friday — should be a quick fix."
- Right: "I'd like to take this on. Next I want to trace how `check()` handles a
  `None` chunk value and report back what I find before opening anything."

### Rule: every assertion travels with its artifact

If I say something happened, the output that shows it goes in the same comment.
When I catch myself adding an intensifier, that is the signal that I am short an
artifact and reaching for emphasis instead.

- Wrong: "I did a careful and thorough investigation and can confirm the crash
  is fully reproducible."
- Right: "Reproduced on 3.2.4; the failing run and a control run with the flag
  removed are both pasted below."

### Rule: name the tool and the extent when I use AI

My workflow is AI-assisted. Where a repo asks for disclosure I give it in the
comment itself, naming the tool and what it did, because a reader of the thread
should not have to dig for it. Where a repo asks for nothing I still do not
pretend to a process I did not follow.

- Wrong: (posting a full repro report with no mention of tooling in a repo whose
  `AI_POLICY.md` requires disclosing all AI use)
- Right: "Disclosure per AI_POLICY.md: I used Claude Code to help work through
  the reproduction and to draft this comment. I ran every command shown here
  myself and I understand the failure I am describing."

### Rule: report the negative result as plainly as the positive one

A reproduction that failed is information, not an embarrassment. I post the
environment, the steps, and what I got instead. I do not quietly retry until
something confirms what I expected.

- Wrong: (saying nothing for three days because the bug did not reproduce, then
  posting only after it finally did)
- Right: "I could not reproduce this on 1.3.1 with the config below — I get the
  correct value instead of the reported one. Full steps and output attached; is
  there a setting I am missing?"

### Rule: my proof is mine, in my words

On a shared issue I post my own reproduction from my own environment. I never
add weight to someone else's work without adding evidence to it.

- Wrong: "Same as above, can confirm on my machine too."
- Right: "Also reproducing, on a different setup than the report above —
  Python 3.12 / macOS rather than 3.11 / Linux. My environment and output
  follow, in case the difference is useful."

## Things I never post

- A deadline, an estimate, or the word "guaranteed".
- A request to be assigned, reserved, or given the issue ahead of anyone else.
- "+1", "any updates?", or a bare "same here" with no evidence attached.
- Flattery about the project as a way of opening. If I have nothing to say about
  the bug yet, I do not comment yet.
- A confirmation whose artifact I have not actually read against the issue's
  stated symptom — the failure I am most likely to make when tired is
  recognising *a* failure and reporting it as *the* failure.
- An apology for asking a question, or a claim of expertise I do not have.
