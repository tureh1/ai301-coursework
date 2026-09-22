# Rubric: is this a good first issue?

<!--
This rubric defines the checks used to decide whether an issue is a good
first contribution. Required checks determine the final verdict. Preferred
checks only help rank issues that have already been accepted.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Maintainer activity | Repo-facts block: last 5 default-branch commit dates and last push date. | Pass if the repository has at least one default-branch commit or push within 180 days of the bundle capture date in eval mode, or within 180 days of today in live mode. | required |
| Repository status | Repo-facts block: archived status and latest release information. | Pass if the repository is not archived. A missing or old release does not fail this check if recent development activity exists. | required |
| Bounded scope | Issue title, body, and comment thread. | Pass if the issue identifies one primary user-visible, developer-visible, documentation, testing, or behavioral outcome. Evaluate the issue by its primary outcome rather than by how many files, components, examples, substeps, suspected causes, or possible implementation approaches are mentioned. Multiple related changes pass when completing them together produces the single outcome described by the issue. Multiple examples of the same missing behavior also pass. Multiple suspected causes or proposed optimizations for the same reported bug also pass. Do not reject an issue merely because implementation may require several changes or investigation. Fail if the issue is explicitly an umbrella or tracking issue for independent work, combines unrelated outcomes that could be completed independently without achieving the others, asks generally for improvements without defining the desired result, or requires an unresolved product/design decision before a contributor can know what result to implement. | required |
| Available to work on | Repo-facts block: assignees and linked pull requests; issue comment thread for explicit work-in-progress or claim comments. | Determine availability from both the repo facts and the chronological comment thread. Pass only if there is no current assignee, no open pull request implementing the issue, and no unresolved indication that another contributor is currently working on it. For claim-based repositories, follow each contributor's claim chronologically: a claim followed by an explicit unassignment, release, abandonment, or equivalent bot message is resolved and does not block the issue; a claim with no later evidence releasing that claim is unresolved and fails this check, even if the repo-facts block currently lists no assignee. Closed or abandoned pull requests do not by themselves fail this check. In Path Review live mode, follow the house rule in scope.md and ignore other students' claim comments. | required |
| Contribution policy | Repo-facts contribution-policy information and, when available in live mode, the contribution or AI-policy locations listed in references/evidence-guide.md. | Pass if no repository policy prohibits the AI-assisted contribution workflow used in this course. Silence about AI passes. A policy that permits AI with requirements such as disclosure, testing, understanding, or human review passes. An explicit prohibition on AI-generated code or documentation fails. | required |
| Newcomer clarity | Issue title, body, labels, and comment thread. | Pass if the issue identifies a concrete problem or desired outcome well enough for a contributor to begin investigating the relevant behavior or area. Detailed implementation instructions are not required. A concise issue can pass when its title and body identify the missing or incorrect behavior. Examples, reproduction information, affected behavior, suspected causes, suggested solutions, or maintainer-applied newcomer labels may provide supporting evidence. A `good first issue` label supports the check but does not automatically make it pass. Fail when the desired outcome itself is too ambiguous to determine what successful completion would mean. | required |

## Verdict rule

Grade each required check from the evidence specified in the rubric.

Accept an issue only if every required check passes.

Reject an issue if any required check fails.

Treat `unclear` as fail because a good first issue should provide enough
evidence to determine that it is suitable.

Do not reject an otherwise bounded issue solely because it touches multiple
files, lists several examples of the same behavior, contains several related
implementation steps, identifies multiple possible causes of one bug, or
suggests multiple approaches for achieving one outcome.

Labels such as `good first issue` are supporting evidence only. They cannot
override a failed required check.