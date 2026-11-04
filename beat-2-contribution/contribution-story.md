# Contribution: Implement Jest Tests for Readme Generator

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** [1]
**Student:** Tu Reh
**GitHub Username:** tureh1
**Issue:** https://github.com/Bashamega/WebDevTools/issues/393
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

**Unit 6: Guarded my upstream claim and cached the Readme generator's test context**

I promoted my voice guide to one canonical voice-guard folder, added rules for claims, questions, and pings, and checked the replacement claim with both repro-check and the guard before posting it through Claude Code. Windows would not create the shared symbolic links from the installer, so I used the documented pointer-file fallback and kept the canonical guide as the source of truth. I also replaced the old repo memory with a local `CLAUDE.local.md` for WebDevTools that records the Readme generator files, Jest conventions, download mocks, and exact checks; `.git/info/exclude` keeps it out of the pull request.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I found this issue on the AI301 Issue Selection site after filtering for Python, JavaScript, and TypeScript work tagged `bug` or `good first issue`. My earlier scout rounds rejected DNA #254, DNA #255, WebDevTools #402, and Meteor #14910 because open pull requests were already doing the work. I initially claimed Nimbus #973, but its repository required assignment before starting and the maintainer did not respond. I then checked HyP3 #2551 and Altinn #14230, but their threads showed earlier human claims, so I graded WebDevTools #393, #401, and #395 as replacements. The scout accepted all three and ranked #393 first with moderate difficulty because it is one interactive React page with a direct mapping from its behavior to Jest tests.

I chose WebDevTools #393 because it is a bounded React testing contribution I can run locally on Windows without paid services or special hardware. It lets me demonstrate Jest, React Testing Library, accessible queries, DOM API mocking, coverage evidence, and developer documentation in one focused pull request. I hope to learn how to test generated iframe content and browser downloads without coupling tests to implementation details. The scout prevented me from competing with several existing contributors, but it missed likely review latency: two similar test pull requests have been waiting since April, so I recorded that limitation and chose the page with the clearest finishable scope. **Claim comment:** https://github.com/Bashamega/WebDevTools/issues/393#issuecomment-6643319296

**Toolkit**

- Tool: issue-scout; I used my fit profile, three GitHub queries, and direct URL grading on each replacement finalist.
- What it produced: It accepted WebDevTools #393, #401, and #395 and ranked #393 first because the Readme generator is one interactive page with concrete rendering, interaction, and state behavior to test.
- Difficulties: Open issues without assignees were often already claimed in comments, and the rubric's activity check did not reveal that similar test PRs had waited months for review.
- What changed: I checked every comment and related PR before claiming, logged review latency as a scout limitation, and selected the clearest scope rather than the most impressive title.

---

## Understanding the Issue

**Problem Description**

The Readme generator has no Jest tests. Its form state, Markdown generation, iframe preview, and download behavior can change without any focused automated test detecting a regression.

**Expected Behavior**

The repository should have passing tests that prove the page renders its controls, responds to user input, updates the generated preview, and creates a Markdown download with the expected filename and content.

**Current Behavior**

The existing Jest suite passes, but it never imports the Readme generator. A coverage run against its page reports 0% for statements, branches, functions, and lines.

**Affected Components**

The main component is `src/app/customizer/Readme-generator/page.jsx`. The new focused test belongs under `__tests__/customizer/Readme-generator/`; `jest.config.js` and `jest.setup.js` provide jsdom, path aliases, and jest-dom matchers, while `contributing.md` needs the requested test-running instructions.

**Toolkit**

- Tool: scoped Claude Code orientation plus direct source reading.
- What it produced: A four-file map of the component, its future test, Jest configuration, and developer documentation, including the DOM APIs that require mocks.
- Difficulties: The issue's template language was broad and did not identify the exact page behavior or existing test conventions.
- What changed: I narrowed the contribution to observable rendering, input, preview, and download behavior and recorded the boundaries in `CLAUDE.local.md`.

---

## Reproduction Process

**Environment Setup**

I used Windows 11 with PowerShell, Node.js v24.12.0, and npm 11.6.2. I forked `Bashamega/WebDevTools`, cloned it with `origin` pointing to my fork and `upstream` pointing to the source repository, then created `test/readme-generator-jest-tests` from upstream commit `b62f4817f2dd24699c1d2038088ff5fb76115eb1`. The first `npm ci` attempt hit a transient OneDrive `EPERM` while creating a nested Jest directory; the same clean install succeeded on retry and installed 1,138 packages.

**Steps to Reproduce**

1. From the repository root, set `$env:NODE_OPTIONS='--experimental-vm-modules'` in PowerShell.
2. Run `npx jest --coverage --collectCoverageFrom='src/app/customizer/Readme-generator/page.jsx' --runInBand` against the untouched upstream commit.
3. Observe that all 8 existing suites and 31 tests pass, while `page.jsx` reports 0% statements, branches, functions, and lines with lines 3–167 uncovered.
4. As a contrast, run coverage for `__tests__/lib/generator/sitemapXml.test.js`; its tested source reports 76.92% statement and line coverage, proving the tool records nonzero coverage when tests exercise a selected file.

**Reproduction Evidence**

- **Commit showing reproduction:** none; reproduction required no code changes and used upstream commit `b62f4817f2dd24699c1d2038088ff5fb76115eb1`.
- **Screenshots/logs:** Jest reported `page.jsx | 0 | 0 | 0 | 0 | 3-167`, followed by `Test Suites: 8 passed, 8 total` and `Tests: 31 passed, 31 total`.
- **My findings:** The issue is reproducible today. Jest itself is working, but none of the current tests import or execute the Readme generator; the contrast run measured substantial coverage for an existing tested module.

**Toolkit**

- Tool: repro-check; I graded the public claim and the pinned reproduction report together against the live issue.
- What it produced: An accepted report with a rerunnable environment, exact commands, a zero-coverage artifact, and an honest conclusion tied to the issue.
- Difficulties: The first draft used an unproved adjective and treated passing suites from the same run as the control; dependency installation also failed once under OneDrive.
- What changed: I removed the unsupported claim, included the exact `EPERM` line, and added a separate contrast coverage run that produced nonzero results for a tested module.

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
