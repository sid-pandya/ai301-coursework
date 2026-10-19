# Issue Scout: Scan Results

Run mode: Live scan
Capture date: 2026-10-18
Queries evaluated: 3
Candidates returned by search: 290
Candidates after free ranking filters: 168
Candidates graded: 5

## Ranked Read-Out

### Accepted Candidates (in fit-profile rank order)

1. https://github.com/yunaremaia/agent-undo/issues/72
   - Title: main branch has unresolved merge conflict markers in cli.py and hooks.py — SyntaxError on import
   - Fit reason: High fit — Python backend bug with clear syntax error fix in cli.py and hooks.py, pytest suite, no frontend/CSS work.
   - Surfaced by query: gh search issues --language python --label "good first issue" --state open --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url
   - Ranking signals: 0 assignees, 0 comments, label "bug", updated within 24 hours.

2. https://github.com/deepsucksi/deepsuck/issues/84
   - Title: [新人任务] 沙盒页「提交到公开情报库」文本框补可访问名称
   - Fit reason: Moderate fit — single contained issue adding accessible label to textarea, but touches site HTML/accessibility rather than Python backend.
   - Surfaced by query: gh search issues --language python --label "good first issue" --state open --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url
   - Ranking signals: 0 assignees, 0 comments, label "type:bug", updated within 24 hours.

3. https://github.com/deepsucksi/deepsuck/issues/83
   - Title: [新人任务] 沙盒页、情报库页、404 页补「跳到正文」链接与主导航 aria-label
   - Fit reason: Low fit — HTML accessibility navigation changes across 3 pages, frontend/site area rather than Python backend.
   - Surfaced by query: gh search issues --language python --label "good first issue" --state open --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url
   - Ranking signals: 0 assignees, 0 comments, label "type:bug", updated within 24 hours.

4. https://github.com/deepsucksi/deepsuck/issues/82
   - Title: [新人任务] 沙盒页「发布策略」新窗口链接补 rel="noopener"，并加测试
   - Fit reason: Low fit — HTML target="_blank" security attribute and JS translation key fix in site/ assets, not Python backend.
   - Surfaced by query: gh search issues --language python --label "good first issue" --state open --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url
   - Ranking signals: 0 assignees, 0 comments, label "type:bug", updated within 24 hours.

### Rejected Candidates

5. https://github.com/aws-samples/sample-aws-genai-db-modernizer/issues/1088
   - Title: [Bug] Pattern analysis page keeps its own engine colour table
   - Sank by: issue-unclaimed (failed — open linked pull request #1105 "fix(ui): read PatternAnalysis engine badge colours from the shared palette" is active). Also touches UI JavaScript (PatternAnalysis.js).
   - Surfaced by query: gh search issues --language python --label "good first issue" --state open --limit 100 --json title,repository,labels,assignees,commentsCount,updatedAt,url
   - Ranking signals: 0 assignees, 0 comments, label "bug", updated within 24 hours.

```json
[
  {
    "item": "https://github.com/yunaremaia/agent-undo/issues/72",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit pushed 2026-10-18, within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repository archived status is false."},
      {"name": "issue-unclaimed", "grade": "pass", "evidence": "Assignees: none, comments: 0, no open linked pull requests."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "0 linked closed unmerged PRs."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Single contained bug removing conflict markers in cli.py and hooks.py to restore clean import."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Opened by repo owner (author_association: OWNER) with labels 'bug' and 'good first issue'."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "No restrictive AI contribution policy; repo includes AI agent workflows."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Maintainer created and labeled issue within the last 24 hours."},
      {"name": "released-recently", "grade": "pass", "evidence": "Release v0.1.0 published on 2026-10-01, within 365 days."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Carries 'good first issue' label."}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/deepsucksi/deepsuck/issues/84",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit pushed 2026-10-19, within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repository archived status is false."},
      {"name": "issue-unclaimed", "grade": "pass", "evidence": "Assignees: none, 0 comments, no open linked PRs (cross-referenced PR #87 is closed)."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "0 closed unmerged fix attempts (PR #87 was task creation)."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Adding aria-label or accessible name to a single textarea element in demo.html."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Opened by repo owner (author_association: OWNER) with labels."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "Repository silence on AI contributions; passes by policy silence rule."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Maintainer actively filing triaged issues today."},
      {"name": "released-recently", "grade": "fail", "evidence": "No published releases found."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Carries 'good first issue' label."}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/deepsucksi/deepsuck/issues/83",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit pushed 2026-10-19, within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repository archived status is false."},
      {"name": "issue-unclaimed", "grade": "pass", "evidence": "Assignees: none, 0 comments, no open linked PRs."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "0 closed unmerged fix attempts."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Adding skip links and aria-label navigation elements across 3 static HTML pages."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Opened by repo owner (author_association: OWNER) with labels."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "Repository silence on AI contributions."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Maintainer actively triaging issues today."},
      {"name": "released-recently", "grade": "fail", "evidence": "No published releases found."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Carries 'good first issue' label."}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/deepsucksi/deepsuck/issues/82",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit pushed 2026-10-19, within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repository archived status is false."},
      {"name": "issue-unclaimed", "grade": "pass", "evidence": "Assignees: none, 0 comments, no open linked PRs."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "0 closed unmerged fix attempts."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Adding rel='noopener' attribute to external links in demo.html and i18n dictionary."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Opened by repo owner (author_association: OWNER) with labels."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "Repository silence on AI contributions."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Maintainer actively triaging issues today."},
      {"name": "released-recently", "grade": "fail", "evidence": "No published releases found."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Carries 'good first issue' label."}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/aws-samples/sample-aws-genai-db-modernizer/issues/1088",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit pushed 2026-10-19, within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repository archived status is false."},
      {"name": "issue-unclaimed", "grade": "fail", "evidence": "Linked pull request #1105 ('fix(ui): read PatternAnalysis engine badge colours from the shared palette') is currently open."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "0 closed unmerged PR attempts (1 active open PR)."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Single component color table consolidation into shared palette."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Carries 'bug' and 'good first issue' labels."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "CONTRIBUTING.md explicitly integrates and permits AI coding workflows (Claude Code/Bedrock)."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "First response within 24 hours on open issues."},
      {"name": "released-recently", "grade": "fail", "evidence": "No published releases."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Carries 'good first issue' label."}
    ],
    "verdict": "reject"
  }
]
```


---

# Direct Grading of Candidate Picks

Run mode: Live direct grading
Date: 2026-10-18
Picks evaluated: 3
Source: AI301 Issue Selection site (filtered for Python, bug, good first issue)

## Candidate Read-Out

### 1. https://github.com/apache/airflow/issues/57864
- Title: Secrets with slashed IDs return AirflowNotFoundException
- Repository: apache/airflow
- Language/Stack: Python (FastAPI / Starlette Execution API)
- Verdict: reject
- Checks failed:
  * `issue-unclaimed`: fail (Linked pull request #74475 "Allow slashed connection IDs in GET and DELETE connection routes" is currently OPEN; also claim comment by HarshXAI on 2026-09-29).
  * `no-abandoned-attempts`: fail (8 closed unmerged pull requests: #58122, #63022, #63103, #65974, #69228, #69439, #69585, #69780, showing high hidden complexity / unsettled design).
- Fit profile: High technical stack alignment with FastAPI web services, but severe claim contention and abandonments.

### 2. https://github.com/apache/beam/issues/19713
- Title: ProtoCoder <T extends Message> throws NoSuchMethodException: com.google.protobuf.Message.getDefaultInstance()
- Repository: apache/beam
- Language/Stack: Java (Beam Java SDK, Protobuf)
- Verdict: reject
- Checks failed:
  * `no-abandoned-attempts`: fail (2 closed unmerged pull requests: #34194 and #37005).
- Fit profile: Low fit — Java SDK and Protobuf reflection work outside the student's Python working language.

### 3. https://github.com/finos/opengris-scaler/issues/366
- Title: PyYMQ exceptions are not serializable
- Repository: finos/opengris-scaler
- Language/Stack: Python, C++ (pickle serialization)
- Verdict: reject
- Checks failed:
  * `issue-unclaimed`: fail (Linked pull request #961 "fix: pickle round trips for PyYMQ exceptions" is currently OPEN with 3 commits and active review).
- Fit profile: High technical fit — single contained Python exception serialization bug with clean reproduction snippet (`pickle.dumps(e)`), but actively worked on by another contributor.

```json
[
  {
    "item": "https://github.com/apache/airflow/issues/57864",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit pushed 2026-10-18, within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repository archived status is false."},
      {"name": "issue-unclaimed", "grade": "fail", "evidence": "Linked pull request #74475 is currently OPEN; claim comment by HarshXAI on 2026-09-29."},
      {"name": "no-abandoned-attempts", "grade": "fail", "evidence": "8 linked PRs closed without merging (#58122, #63022, #63103, #65974, #69228, #69439, #69585, #69780)."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "URL decoding of connection ID path parameters in FastAPI execution API route."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Carries labels 'kind:bug', 'area:API', 'good first issue', 'area:core'."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "Apache Software Foundation allows AI assistance with contributor responsibility."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "High maintainer activity on recent issues and PRs."},
      {"name": "released-recently", "grade": "pass", "evidence": "Release 3.3.2 published 2026-09-17, within 365 days."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Carries 'good first issue' label."}
    ],
    "verdict": "reject"
  },
  {
    "item": "https://github.com/apache/beam/issues/19713",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit pushed 2026-10-19, within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repository archived status is false."},
      {"name": "issue-unclaimed", "grade": "pass", "evidence": "Assignees: none, no open linked PRs, 0 claim comments."},
      {"name": "no-abandoned-attempts", "grade": "fail", "evidence": "2 linked PRs closed without merging (#34194 and #37005)."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Handling getDefaultInstance NoSuchMethodException in ProtoCoder."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Carries labels 'java', 'bug', 'good first issue', 'P3'."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "Apache Software Foundation allows AI assistance with contributor responsibility."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Active maintainers responding across repo issues."},
      {"name": "released-recently", "grade": "pass", "evidence": "Release v2.77.0 published 2026-10-07, within 365 days."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Carries 'good first issue' label."}
    ],
    "verdict": "reject"
  },
  {
    "item": "https://github.com/finos/opengris-scaler/issues/366",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit pushed 2026-10-16, within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repository archived status is false."},
      {"name": "issue-unclaimed", "grade": "fail", "evidence": "Linked pull request #961 ('fix: pickle round trips for PyYMQ exceptions') is currently open."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "0 closed unmerged PR attempts."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Single contained serialization method for YMQException to support pickle.dumps()."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Opened by COLLABORATOR with labels 'good first issue', 'python', 'c++'."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "FINOS policy permits contributions conforming to standard contributor license."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Maintainer comments within response window."},
      {"name": "released-recently", "grade": "pass", "evidence": "Release v2.9.0 published 2026-10-08, within 365 days."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Carries 'good first issue' label."}
    ],
    "verdict": "reject"
  }
]
```


---

# Direct Grading of Candidate Picks

Run mode: Live direct URL grading
Capture date: 2026-10-18
Candidates graded: 5

## Ranked Read-Out

### Accepted Candidates (in fit-profile rank order)

1. https://github.com/ArduPilot/ardupilot/issues/14438
   - Title: Modify autotest to notify Discord when run is complete
   - Fit reason: Low fit — although it passes required rubric checks (unassigned, no open PR, bounded autotest notification), the repository is a massive C++ autopilot codebase and asks for open-ended external webhook notification rather than a contained Python backend bug.
   - Surfaced by: Direct URL input
   - Ranking signals: direct

### Rejected Candidates

2. https://github.com/apache/hamilton/issues/54
   - Title: Add testing for thread safety of driver
   - Sank by: issue-unclaimed (failed — pull request #1686 'test(driver): add testing for thread safety of driver (#54)' is currently open and active).
   - Surfaced by: Direct URL input
   - Ranking signals: direct

3. https://github.com/LMCache/LMCache/issues/5403
   - Title: [good-first-issue] tests: preserve phase timings for flaky long_doc_qa TTFT checks
   - Sank by: issue-unclaimed (failed — issue is closed; pull request #5438 was already merged on 2026-10-14).
   - Surfaced by: Direct URL input
   - Ranking signals: direct

4. https://github.com/ESCOMP/CTSM/issues/2497
   - Title: Use full MPI library for mpi-serial tests
   - Sank by: scope-bounded (failed — carrying label 'blocked: dependency'; requires upstream external library changes before tests can be converted. Also in Fortran).
   - Surfaced by: Direct URL input
   - Ranking signals: direct

5. https://github.com/ArduPilot/ardupilot/issues/19190
   - Title: Write autotest for the no-short-failsafe option in auto mode for Plane
   - Sank by: issue-unclaimed and no-abandoned-attempts (failed — pull request #32503 was already merged to implement this autotest; also carries 2 closed unmerged pull requests #19716 and #31383).
   - Surfaced by: Direct URL input
   - Ranking signals: direct

```json
[
  {
    "item": "https://github.com/ArduPilot/ardupilot/issues/14438",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit pushed 2026-10-18, within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repository archived status is false."},
      {"name": "issue-unclaimed", "grade": "pass", "evidence": "Assignees: none, no open linked PRs (PR #31900 closed unmerged)."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "1 closed unmerged PR (#31900), fewer than 2."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Adding a post-test Discord webhook ping to the existing autotest harness."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Opened by maintainer (peterbarker) with label 'good first issue'."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "Contributing guide silence on AI contributions; passes by policy silence rule."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Maintainers active across repository issues this week."},
      {"name": "released-recently", "grade": "pass", "evidence": "Plane-4.7.2 published 2026-10-14, within 365 days."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Carries 'good first issue' label."}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/apache/hamilton/issues/54",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit pushed 2026-09-29, within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repository archived status is false."},
      {"name": "issue-unclaimed", "grade": "fail", "evidence": "Linked pull request #1686 ('test(driver): add testing for thread safety of driver') is currently open."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "0 closed unmerged PR attempts."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Adding pytest suite verifying driver execution across worker threads."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Carries label 'good first issue'."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "Standard Apache license agreement; silence on AI usage."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Maintainers actively reviewing PRs and issues."},
      {"name": "released-recently", "grade": "pass", "evidence": "apache-hamilton-v1.90.0-incubating-RC0 published 2026-04-25, within 365 days."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Carries 'good first issue' label."}
    ],
    "verdict": "reject"
  },
  {
    "item": "https://github.com/LMCache/LMCache/issues/5403",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit pushed 2026-10-19, within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repository archived status is false."},
      {"name": "issue-unclaimed", "grade": "fail", "evidence": "Issue is closed; pull request #5438 ('[Bugfix] test(ci): preserve long-doc QA phase timings') was merged 2026-10-14."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "0 closed unmerged PRs (1 merged PR)."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Preserving phase timing metadata in CI benchmark runner."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Opened by maintainer (maobaolong) with label 'good first issue'."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "DCO sign-off required; silence on AI assistance."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Active maintainer responses within 48 hours."},
      {"name": "released-recently", "grade": "pass", "evidence": "Release v0.5.6 published 2026-10-14, within 365 days."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Carries 'good first issue' label."}
    ],
    "verdict": "reject"
  },
  {
    "item": "https://github.com/ESCOMP/CTSM/issues/2497",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit pushed 2026-10-16, within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repository archived status is false."},
      {"name": "issue-unclaimed", "grade": "pass", "evidence": "Assignees: none, 0 open linked PRs."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "0 closed unmerged PRs."},
      {"name": "scope-bounded", "grade": "fail", "evidence": "Explicitly labelled 'blocked: dependency' and body notes work depends on getting mpi-serial changes into external library."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Opened by collaborator (ekluzek) with label 'good first issue'."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "Standard open source contributor workflow; silence on AI."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Maintainers active on repository."},
      {"name": "released-recently", "grade": "pass", "evidence": "Release ctsm5.4.002 published 2025-12-12, within 365 days."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Carries 'good first issue' label."}
    ],
    "verdict": "reject"
  },
  {
    "item": "https://github.com/ArduPilot/ardupilot/issues/19190",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit pushed 2026-10-18, within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repository archived status is false."},
      {"name": "issue-unclaimed", "grade": "fail", "evidence": "Pull request #32503 was already merged on 2026-03-20 to implement the requested autotest."},
      {"name": "no-abandoned-attempts", "grade": "fail", "evidence": "Two closed unmerged pull requests (#19716, #31383) show prior stalled attempts."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Adding a single autotest for Plane failsafe behavior."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Opened by maintainer (peterbarker) with label 'good first issue'."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "Contributing guide silence on AI contributions."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Maintainers actively commenting and triaging."},
      {"name": "released-recently", "grade": "pass", "evidence": "Plane-4.7.2 published 2026-10-14, within 365 days."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Carries 'good first issue' label."}
    ],
    "verdict": "reject"
  }
]
```

---

# Direct Grading: Issue Tracker Picks

Run mode: Live direct grading
Date: 2026-10-18
Picks evaluated:
- https://github.com/GRCEngClub/claude-grc-engineering/issues/160
- https://github.com/HHS/simpler-grants-gov/issues/9817
- https://github.com/Cog-Creators/Red-DiscordBot/issues/6794

## Summary & Ranked Read-Out

1. https://github.com/GRCEngClub/claude-grc-engineering/issues/160
   - Title: Add v1 finding fixtures for okta-inspector
   - Verdict: **ACCEPT**
   - Fit reason: High fit for newcomer. Clean, mechanical task creating 3 JSON finding fixtures validated against `tests/validate-contract-fixtures.sh`. Assignees: none, open linked PRs: none, active maintainer response.

2. https://github.com/HHS/simpler-grants-gov/issues/9817
   - Title: Add `ValidationErrorDetail` to two saved-search/saved-opportunity delete service
   - Verdict: **REJECT**
   - Sank by: `issue-unclaimed` (PR #10710 is currently open) and `no-abandoned-attempts` (3 closed unmerged attempts: #10390, #10691, #10735).
   - Fit reflection: Exceptionally high language fit (Python, Flask, API error contract, pytest unit tests). If chosen despite scout rejection, this provides a great reflection in `contribution-story.md` explaining how the scout caught active competition.

3. https://github.com/Cog-Creators/Red-DiscordBot/issues/6794
   - Title: [Audio] Track Error report too long
   - Verdict: **REJECT**
   - Sank by: `issue-unclaimed` (4 open competing pull requests: #6795, #6796, #6801, #6810).
   - Fit reflection: Contained string truncation bug in Python async event handler, but heavily congested with outside attempts.

```json
[
  {
    "item": "https://github.com/GRCEngClub/claude-grc-engineering/issues/160",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest default-branch commit pushed 2026-08-19, within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "Repository archived is False."},
      {"name": "issue-unclaimed", "grade": "pass", "evidence": "Assignees: none; open linked PRs: none."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "Closed linked PRs: 1."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Single bounded task adding 3 finding fixtures with mechanical test verification."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Carries 'good first issue', 'connector', 'difficulty: easy' labels; author_association: COLLABORATOR."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "No prohibition on AI-assisted contributions in repository policy."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Repository shows active maintainer commit and triage within 45 days."},
      {"name": "released-recently", "grade": "pass", "evidence": "Latest release published 2026-04-30, within 365 days."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Labels include 'good first issue'."}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/HHS/simpler-grants-gov/issues/9817",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest commit date 2026-10-16 is within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "archived is False."},
      {"name": "issue-unclaimed", "grade": "fail", "evidence": "Assignees: none; open linked PR #10710 is currently open."},
      {"name": "no-abandoned-attempts", "grade": "fail", "evidence": "Three closed unmerged PRs (#10390, #10691, #10735) indicate stalled previous attempts."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Single bounded issue adding ValidationErrorDetail to two delete service functions."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Labels: ['good first issue', 'help wanted', 'api']; author_association: COLLABORATOR."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "No prohibition on AI-assisted contributions in repository policy."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Repository shows active maintainer commit and triage within 45 days."},
      {"name": "released-recently", "grade": "pass", "evidence": "Latest release date 2026-10-14, within 365 days."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Labels include 'good first issue' and 'help wanted'."}
    ],
    "verdict": "reject"
  },
  {
    "item": "https://github.com/Cog-Creators/Red-DiscordBot/issues/6794",
    "checks": [
      {"name": "repo-active", "grade": "pass", "evidence": "Newest commit date 2026-10-18 is within 180 days."},
      {"name": "repo-not-archived", "grade": "pass", "evidence": "archived is False."},
      {"name": "issue-unclaimed", "grade": "fail", "evidence": "Four competing open pull requests exist: #6795, #6796, #6801, #6810."},
      {"name": "no-abandoned-attempts", "grade": "pass", "evidence": "Closed linked PRs: 0."},
      {"name": "scope-bounded", "grade": "pass", "evidence": "Single bounded issue to truncate or format long track error message."},
      {"name": "maintainer-triaged", "grade": "pass", "evidence": "Labels include 'Type: Bug', 'Good First Issue'; author_association: MEMBER."},
      {"name": "ai-policy-permits", "grade": "pass", "evidence": "No prohibition on AI-assisted contributions in repository policy."},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Maintainer activity within 45 days."},
      {"name": "released-recently", "grade": "pass", "evidence": "Release v3.5.24 published 2026-03-06, within 365 days."},
      {"name": "newcomer-labelled", "grade": "pass", "evidence": "Labels include 'Good First Issue'."}
    ],
    "verdict": "reject"
  }
]
```
