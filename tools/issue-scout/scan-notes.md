# Scan Notes: Issue Scout Iteration Log

2026-10-18: Ran initial live scan across GitHub using three fit-profile queries (general python newcomer issues, FastAPI queries, and python bug issues). The ranking filters narrowed 290 candidates down to 168 survivors and selected the top 5 for grading: yunaremaia/agent-undo#72, deepsucksi/deepsuck#84, #83, #82, and aws-samples/sample-aws-genai-db-modernizer#1088.
- What broke / fell short: The broad Python language search let through three HTML accessibility issues from a polyglot repo (`deepsucksi/deepsuck` labeled `area:site` and `accessibility`) and one UI JavaScript badge palette issue (`aws-samples/sample-aws-genai-db-modernizer#1088`), which contradict the fit profile's preference for Python backend bugs and avoidance of frontend/CSS.
- Rubric performance: The rubric correctly evaluated all 5 candidates: agent-undo#72 passed all checks with high fit; deepsuck issues passed rubric checks but ranked low on fit; sample-aws-genai-db-modernizer#1088 was correctly rejected by `issue-unclaimed` because PR #1105 was already open for it.
- Fix applied: Updated ranking filter 2 in `search-strategy.md` to drop results whose labels or title include "accessibility", "site", "html", or "javascript" alongside "frontend", "css", "ui", and "documentation", preventing site/frontend tasks in polyglot Python repositories from crowding out pure Python backend issues.

2026-10-18: Ran direct grading over 5 candidate picks from the Issue Selection site (ArduPilot/ardupilot#19190, ArduPilot/ardupilot#14438, ESCOMP/CTSM#2497, LMCache/LMCache#5403, and apache/hamilton#54).
- Rubric findings: Four candidates were rejected for critical real-world friction that the issue title alone concealed:
  * ArduPilot/ardupilot#19190: Already solved by merged PR #32503; failed `issue-unclaimed` and `no-abandoned-attempts` (two closed unmerged PRs #19716, #31383).
  * apache/hamilton#54: Failed `issue-unclaimed` because PR #1686 is currently open and active.
  * LMCache/LMCache#5403: Failed `issue-unclaimed` because the issue was closed and PR #5438 was already merged.
  * ESCOMP/CTSM#2497: Failed `scope-bounded` because it is labeled `blocked: dependency` and depends on upstream MPI library refactoring.
- Accepted candidate: ArduPilot/ardupilot#14438 passed all required checks, but ranks low on fit against the Python backend profile due to being in a C++ autopilot codebase and involving open-ended Discord webhook design.
