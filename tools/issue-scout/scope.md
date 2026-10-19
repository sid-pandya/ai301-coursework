# Scope: where to look, and who is looking

<!--
This is the real-world scope. It replaces the sandbox scope.md in
your installed skill: the field is no longer the Path Review repo,
and the house rules do not travel. Like every scope file, this one is
the skill's field of view: the rubric (rubric.md) decides whether an
issue is GOOD; the scope decides which issues are candidates at all,
and the strategy (search-strategy.md) decides where the scout
actually walks. Live mode only: in eval mode the bundle is the whole
world and this file is ignored.
-->

## Where candidates come from

Any public repository. The sandbox is behind you; the field is now
all of GitHub, and your fit profile below is what keeps that
searchable. Start from repos in languages and tools you named there,
and let the profile steer where you search, not just how results are
ranked.

**Claims count again.** The Path Review house rule does not travel.
Out here a claim comment or an assignee is a stranger who got there
first, and you have no channel to coordinate with them. Read claim
signals exactly as the rubric's checks state them; do not carry the
classroom habit of claiming over someone.

**Claim upstream the moment you commit.** The rule cuts both ways:
the instant you decide to take an issue, say so on the issue itself
(a short comment: what you plan to do and roughly when). You are now
the claim signal someone else's skill will read. Deciding privately
and claiming later is how two strangers do the same work twice.

## Your fit profile

<!-- Paste this forward from your sandbox scope.md, and update it:
it now does more work. Besides ranking the issues your rubric
accepts, it steers WHERE the scout searches: your search-strategy.md
queries should each trace to a sentence here, so name the languages,
tools, and project areas you actually want to work in, and anything
you want to avoid. It still never changes a verdict: fit cannot
rescue an issue your rubric rejects, and cannot sink one it
accepts. -->

Python is my working language. I have built web services with Flask and
FastAPI backed by SQLAlchemy, and I have done retrieval-augmented
generation work with embeddings, a Chroma vector store, and Hugging Face
transformers, including PDF text extraction and chunking on the ingestion
side. I read and write pytest tests comfortably, and I am used to working
from a failing test back to the code that caused it.

What I want to get better at is contributing to a codebase I did not
write: finding the existing test that covers a behaviour, matching the
conventions already in the file, and keeping a change small enough that a
maintainer can review it quickly.

What I would rather avoid is frontend and CSS work, and large open-ended
feature design. I would much rather fix one specific wrong behaviour that
has a clear right answer than add a new subsystem. Given that, rank
smaller and more contained issues above larger ones: a single failing
function in the Python backend, ideally in retrieval, ingestion, or the
test suite, ranks highest for me.
