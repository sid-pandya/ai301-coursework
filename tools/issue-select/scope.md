# Scope: where to look, and who is looking

<!--
This file is the skill's field of view. The rubric (rubric.md) decides
whether an issue is GOOD; the scope decides which issues are candidates
at all, and whose hands the issue would land in. It applies in live mode
only: in eval mode the bundle is the whole world and this file is
ignored.

Two parts. Staff wrote the first; you write the second.
-->

## Where candidates come from

Only issues in the course's Path Review repository are candidates:

- Repo: `codepath/pathreview-ai301-fa26-s3`

Do not search, fetch, or grade issues from any other repository, however
promising. The wider GitHub comes later in the course; for now the field
is Path Review.

**Path Review house rule.** Path Review is a classroom, and your
classmates are not strangers. Ignore the usual claim signals here: other
students' claim comments (and there may be several on one issue) do not
block an issue, and finding some on the issue you want is normal. Claim
anyway: course credit attaches to the pull request you open, not to
whether it merges, so a shared issue costs nobody anything. Everything
else in the rubric applies as written.

## Your fit profile

<!-- YOU write this part: a few sentences about you. What languages and
tools you have actually used, what you want to get better at, anything
you want to avoid. The skill uses this only to RANK the issues your
rubric accepts, never to change a verdict: fit cannot rescue an issue
your rubric rejects, and cannot sink one it accepts. -->

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
