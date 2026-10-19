# Rubric: is this a good first issue?

Every check below names a source a grader can open, and a pass condition
someone else could apply and get the same answer. Recency thresholds are
measured against the bundle's `captured:` date in eval mode, and against
today in live mode.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| `repo-active` | The "last 5 default-branch commits" list in the Repo facts block; take the newest commit date. | The newest default-branch commit is dated within 180 days of the capture date. | required |
| `repo-not-archived` | The `archived:` field on the repo line of the Repo facts block. | The field reads `archived: no`. An archived repo is read-only and cannot take a pull request. | required |
| `issue-unclaimed` | The "this issue: assignees: ...; linked PRs: ..." line in the Repo facts block, plus the dates on any claim comments in the Comments section. | Assignees is `none`, no linked PR is in state `open`, and no comment claims the work dated within 180 days of the capture date. An older claim with no open linked PR is stale and does not block: someone who said "I'll take this" years ago and never opened a pull request has left the issue free. In live mode the scope file's house rule governs claim comments and overrides this clause. | required |
| `no-abandoned-attempts` | The "linked PRs:" entries in the Repo facts block, counting those in state `closed` (closed without merging). | Fewer than 2 linked PRs are in state `closed`. Two or more closed unmerged attempts is evidence the work is harder than the issue text admits. | required |
| `scope-bounded` | The issue title and body, and the comment thread. | The issue asks for work one newcomer could land in a single pull request. It fails on exactly three things: the body is a tracking list that points at other issues or pull requests to be completed separately; it is a pure usage or support question rather than a change to the project; or a maintainer says in the thread that the fix needs changes to core internals, or that the design is still unsettled. Nothing else fails it. Several examples of one repetitive change, an acceptance-criteria checklist, a bare title, or a body with no reproduction steps are all bounded: grade the size of the work asked for, not the length or polish of the writeup. | required |
| `maintainer-triaged` | The issue's `labels:` field and the `author_association` on the opening post. | The issue carries at least one label, or was opened by an account with association OWNER, MEMBER, or COLLABORATOR. An unlabelled issue from an outside account is one no maintainer has yet agreed is real work. | required |
| `ai-policy-permits` | The "contribution policy" line in the Repo facts block, including any file it names. | The policy does not refuse AI-assisted contributions. Silence passes. Conditions such as disclosure, human review, personal understanding, or testing pass, because they are terms to follow. A statement that AI-generated code or documentation is not accepted fails. | required |
| `maintainer-responsive` | The "maintainer first-response sample" list in the Repo facts block. | At least one sampled issue shows a first owner, member, or collaborator response within 45 days. | preferred |
| `released-recently` | The "latest release" entry in the Repo facts block. | A release is published and dated within 365 days of the capture date. | preferred |
| `newcomer-labelled` | The issue's `labels:` field. | The labels include one of `good first issue`, `help wanted`, `easy`, or an equivalent newcomer marker. | preferred |

## Verdict rule

Accept if and only if every `required` check grades `pass`. A single
required `fail` rejects the issue.

`preferred` checks never change a verdict. They rank the issues that were
accepted: among accepted candidates, more passing preferred checks ranks
higher.

`unclear` counts as `fail`. A first issue whose evidence cannot be
verified is not a first issue worth taking.

## Why responsiveness and releases are preferred, not required

Both measure the repo rather than the issue, and both go blank on small
or young projects for reasons that have nothing to do with whether a
newcomer's pull request would land. A repo can publish no releases at all
and still merge contributions weekly; a response sample can be one issue
deep and say nothing either way. The two required repo-level checks,
`repo-active` and `repo-not-archived`, already catch a repository that is
genuinely dead, so making these two required would only add false
rejections without catching anything new.
