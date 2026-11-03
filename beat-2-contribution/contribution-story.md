# Contribution: Clean up leaked temp directories in 3 gateway test files

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Tu Reh
**GitHub Username:** tureh1
**Issue:** https://github.com/nimbus-agent/Nimbus/issues/973
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Built and tested an issue scout against live GitHub results**

I built an issue scout around my Python/backend and React/TypeScript fit profile, with three GitHub queries and ranking rules that graded the top five candidates. Its first live scan accepted four issues and rejected one, but recency pushed brand-new solo repositories to the top and the availability check did not explicitly catch work already completed by a merged pull request. I patched that check so a merged PR that implemented the issue now makes the issue unavailable, and recorded the remaining ranking and language limitations in `scan-notes.md`.

**Unit 6: Added a voice guard and repo-specific memory for upstream work**

I promoted my voice guide to one canonical voice-guard folder, added rules for claims, questions, and pings, and checked my claim draft with both repro-check and the guard before posting it through Claude Code. Windows would not create the shared symbolic links from the installer, so I used the documented pointer-file fallback and kept the canonical guide as the source of truth. I also created a local `CLAUDE.local.md` for Nimbus that records its contribution rules, the four files relevant to the database-test leak, and the exact Bun checks; `.git/info/exclude` keeps that memory out of the pull request.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I found this issue on the AI301 Issue Selection site after filtering for Python, JavaScript, and TypeScript work tagged `bug` or `good first issue`. My scout first graded DNA #254, DNA #255, and WebDevTools #402, but rejected all three because open pull requests were already doing the work. I then graded Nimbus #973, Kaoto #3445, and Meteor #14910. It accepted Nimbus #973 as straightforward, accepted Kaoto #3445 as ambitious because maintainers disagreed about the solution, and rejected Meteor #14910 because it already had an active pull request.

I chose Nimbus #973 because it is a bounded TypeScript testing fix I can reproduce on Windows without a paid service or special hardware. The issue names the affected file, supplies an existing cleanup pattern, and requires a measurable before-and-after count, so the finished contribution can show both implementation and evidence. I hope to learn Bun's test lifecycle hooks and better cross-platform temporary-file hygiene. The scout was useful at finding hidden work in open pull requests, but its first scan overvalued brand-new repositories and a language match did not always mean the issue itself used that language; I recorded both limitations and tightened the availability check. **Claim comment:** https://github.com/nimbus-agent/Nimbus/issues/973#issuecomment-6615026042

**Toolkit**

- Tool: issue-scout; I used my fit profile, three GitHub queries, ranking rules, and direct URL grading on the site finalists.
- What it produced: It accepted Nimbus #973 with a straightforward difficulty note and evidence that the repository was active, the work was unclaimed, AI assistance was allowed, and the acceptance test was concrete.
- Difficulties: The first ranking favored brand-new repositories, and issue pages without assignees still had active pull requests that made the work unavailable.
- What changed: I patched the availability check to reject already-implemented issues, logged the search limitations, and re-picked instead of competing with an existing contributor.

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

[What a stranger would need to stand this up: your operating system,
the relevant versions (language, runtime, package manager, the project
itself), and how you set the project up. Then the challenges you faced
and how you solved them, or that none came up.]

**Steps to Reproduce**

1. [Step 1]
2. [Step 2]
3. [Observed result]

**Reproduction Evidence**

- **Commit showing reproduction:** [link to commit in your fork]
- **Screenshots/logs:** [if applicable]
- **My findings:** [what you discovered during reproduction; an
  honest, evidenced cannot-reproduce that led you to re-pick or ask
  the maintainer belongs here and costs you nothing]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
