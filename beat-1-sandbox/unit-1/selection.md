# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61

**Verdict output**

Accepted, in fit order

1. #61 — Health check DB probe passes a raw SQL string, fails under SQLAlchemy 2.x
Dead center of the stated stack: FastAPI route + SQLAlchemy 2.x + PostgreSQL. The body names the file, the exact exception text, and the fix shape (sqlalchemy.text()), so "successful completion" is unambiguous. It's also the cleanest field of the three — no comments, no claims, no assignee, no prior attempt of any kind. Independently corroborated as live: the repro posted on #62 shows "postgres": "unhealthy" in the same /health response, which is this bug.

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61",
  "checks": [
    {"name": "Maintainer activity", "grade": "pass", "evidence": "Last main commit 2026-09-16 by Andrew Burke; last push 2026-09-16T21:48:27Z — 6 days before today, well within 180 days."},
    {"name": "Repository status", "grade": "pass", "evidence": "GitHub API reports archived=false, disabled=false; 0 releases does not fail given commits 6 days ago."},
    {"name": "Bounded scope", "grade": "pass", "evidence": "One outcome: wrap the literal \"SELECT 1\" in api/routes/health.py in sqlalchemy.text() so the DB probe stops failing."},
    {"name": "Available to work on", "grade": "pass", "evidence": "assignees: none; zero comments; timeline shows only label events, no cross-referenced or connected PRs."},
    {"name": "Contribution policy", "grade": "pass", "evidence": "docs/CONTRIBUTING.md has 0 case-insensitive matches for AI/ChatGPT/Claude/Copilot; PR template requires only CI, tests, lint, typecheck."},
    {"name": "Newcomer clarity", "grade": "pass", "evidence": "Body names the file, the repro (GET /health), and the exact error: \"ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')\"."}
  ],
  "verdict": "accept"
}
```

---

## Eval iterations

**Run history**

- 2/3
- 1/1
- 16/20
- 1/3
- 1/3
- 3/3
- 1/1
- 20/20
- 20/20

The final saved run reported:

`agreement: 20/20 scored items  (bar: 18/20: PASS)`

**Issue analysis**

`issue-15` — My rubric initially returned `accept`, while the gold label was `reject`.

The issue looked available at first because the repo-facts block showed no current assignee and its linked pull requests were closed. Looking through the full comment history showed why that result was wrong. The thread ended with an unresolved `@zulipbot claim` from `souvik150`, with no later unassignment or release in the provided evidence. My original availability check did not make the chronological handling of claims explicit enough, so the model accepted the issue. I revised the check so an unresolved claim fails availability while historical claims that were later released or unassigned do not.

**Check rationale**

Current check from my rubric:

> | Available to work on | Repo-facts block: assignees and linked pull requests; issue comment thread for explicit work-in-progress or claim comments. | Determine availability from both the repo facts and the chronological comment thread. Pass only if there is no current assignee, no open pull request implementing the issue, and no unresolved indication that another contributor is currently working on it. For claim-based repositories, follow each contributor's claim chronologically: a claim followed by an explicit unassignment, release, abandonment, or equivalent bot message is resolved and does not block the issue; a claim with no later evidence releasing that claim is unresolved and fails this check, even if the repo-facts block currently lists no assignee. Closed or abandoned pull requests do not by themselves fail this check. In Path Review live mode, follow the house rule in scope.md and ignore other students' claim comments. | required |

I wrote the check this way because the assignee field by itself did not always show whether someone was actively working on an issue. The comment history can contain claim and unassignment events that change the issue's availability. Following those events chronologically makes the decision more accurate while avoiding rejection because of an old claim that was already released.

**Trade-offs**

This change specifically changed the result for `issue-15`. Before the change, my rubric accepted it even though the gold label was reject. I re-ran only `issue-15` after changing the availability check, and it changed to `reject`, matching the gold label at `1/1`. The trade-off is that the check depends on the available comment history clearly showing whether a claim was later released. If that evidence is missing or incomplete, the conservative `unclear = fail` rule can reject an issue that may actually be available.

---

## Selection rationale

**Selection rationale**

1. I chose issue #61 because it matches the type of backend work I am interested in. I have experience with FastAPI, PostgreSQL, APIs, and working with existing backend code. The issue is also small and clearly defined, so it looks realistic to complete within the time available instead of choosing a larger feature.

2. The verdict correctly identified that the repository is active, the issue has one clear outcome, nobody is currently assigned to it, and the issue gives a specific error and location to investigate. I also considered my own familiarity with FastAPI and backend development. The rubric can determine whether an issue is suitable, but it cannot fully determine how comfortable I personally will be understanding and testing the code.

3. I expect claiming this issue to be fairly straightforward because it currently has no assignee, comments, claims, or open pull request connected to it. The main difficulty is that another student could claim it before I do, so I will need to check its current status again when I reach the claiming step in Unit 2.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.