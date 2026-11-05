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
**Pull Request:** https://github.com/Bashamega/WebDevTools/pull/692

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

**Unit 7: Wrote a Windows-ready ship manifest and passed the ship-gate on my first full run**

My manifest lists WebDevTools' three CI checks from `jest_tests.yml` and `format.yml`, rewritten in cmd.exe form (`set NODE_OPTIONS=--experimental-vm-modules&& npx jest --runInBand`), because the ship-gate runs each row in a fresh Windows shell where `npm test`'s Unix syntax fails. My first manifest moved the repo-wide Prettier check to the excluded table, since it failed on untouched main in 190 files. Then the same check blocked my commit through the husky hook, and I traced both failures to CRLF line endings from `core.autocrlf=true`. After I set `core.autocrlf=false` in my clone, the full `npm run format:check` passed, and I moved it back into the must-pass table. The ship-gate then passed: all three repo checks were ok, and pr-precheck passed all seven checks. It also flagged that my skill's SKILL.md still describes the sandbox `scope.md`, which I noted to fix before unit 9.

**Unit 8: Installed the PR monitor and wrote a playbook from WebDevTools' real review times**

I wrote my playbook's staleness threshold from data rather than a guess: on the last five merged PRs from outside contributors (#581, #452, #441, #440, #423), the owner first responded after 0, 1, 6, 6, and 1 days, so I set 10 quiet days before one nudge and 45 days before walking away. On PR #692, an hour after opening, the run report labeled the state FEEDBACK-IN because Vercel and CodeRabbit had commented, but the drafted move was "no action — wait" because my playbook says bot comments don't count. I didn't post anything. The mismatch showed me the monitor's state label counts any comment, so I'll read the drafted move and the thread, not the label, before deciding. After Bashamega merged the PR the next day, the monitor drafted a thank-you that hedged on whether #393 had closed, so I checked the issue myself, cut that line, and shortened the thanks to one specific sentence.

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

The gap is in the test tree, not the component. Jest collects files under `__tests__/`, and that folder held only `gh-finder/page.test.jsx`, `lib/generator/*`, and `test.test.jsx`, so nothing imported `src/app/customizer/Readme-generator/page.jsx` and its coverage stayed at 0%. The untested behavior lives in four places in `page.jsx`: six `useState` hooks bound to labeled inputs; `generateMarkdown()`, which trims badge lines, drops blank ones, and assembles the sections; the `Parsed Markdown` iframe, whose `srcDoc` runs that Markdown through `snarkdown` and switches colors on `isDarkMode`; and `downloadReadme()`, which wraps the Markdown in a `text/markdown` Blob, sets an anchor's `href` from `URL.createObjectURL`, names it `README.md`, and clicks it. The theme toggle is reached through the shared `NavBar`, which also writes `theme` to `localStorage`. `contributing.md` also had no instructions for running tests, which the issue asks for.

**Proposed Solution**

Add one test file, `__tests__/customizer/Readme-generator/page.test.jsx`, that renders the real page with React Testing Library and checks only what a user can observe: rendering, typing, the preview, the theme toggle, and the downloaded file's name and contents. Mock only what jsdom lacks (`URL.createObjectURL` and anchor navigation) and restore it after each test. Then add a "Run the Tests" step to `contributing.md`. I am deliberately not changing `page.jsx` (its `ButtonCustomizer` export name, the anchor it never removes, the stylesheet link in the preview), `NavBar`, the Jest config, or any dependency.

**Implementation Plan**

1. Create `__tests__/customizer/Readme-generator/page.test.jsx`, importing the page through the `@/` alias and clearing `localStorage` before each test.
2. Rendering test: heading, six empty labeled fields, the Download README button, and the preview iframe.
3. Interaction tests: typing updates each field, and the iframe `srcdoc` shows the title as `<h1>` and each section's text.
4. Download test: mock `URL.createObjectURL` and `HTMLAnchorElement.prototype.click`, then assert one click, `download="README.md"`, and a `text/markdown` Blob whose text equals the expected Markdown, with badges trimmed and the blank line dropped.
5. Theme test: the navbar toggle switches the preview to `#1a202c` and white text and saves `theme` as `"true"`.
6. Add the "Run the Tests" step to `contributing.md`, including the PowerShell form of `NODE_OPTIONS`.
7. Rerun the reproduction's coverage command, the full suite, the coverage run, and Prettier.

Deviations (2026-11-04): none in the code; the real `NavBar` worked under jsdom, so I did not need to mock it. Outside the diff, the repo's husky pre-commit hook failed on all 190 files because Windows had checked them out with CRLF endings. I set `core.autocrlf=false` in my clone only, which made the repo-wide Prettier check pass. The badge-trimming check ended up inside the download test, because the raw Markdown is easier to assert there than in the HTML preview.

**Toolkit**

- Tool: plan-check; I graded `plan.md` and a draft plan comment against issue #393 with `claude -p "plan-check: ..."` from my clone.
- What it produced: An accept verdict with all six required checks passing (diagnosis-grounded, scope-bounded, executable, test-decisive, thread-aware, repo-policy) and one non-blocking fail: "Plan names no risk or unknown."
- Difficulties: The first run refused to read its own skill folder in non-interactive mode and returned an empty reject; the second run failed because `--allowedTools` swallowed my prompt as a tool name. It also flagged that my procedure assumes a posted repro comment, which this issue doesn't have.
- What changed: I reran it with the prompt first plus `--add-dir` for the skills folder, and added an "Unknowns and risks" section to `plan.md` (the `NODE_OPTIONS` dependency, `NavBar` under jsdom, and `Blob.text()` support).

---

## Testing Strategy

I reran the reproduction command, `npx jest --coverage --collectCoverageFrom='src/app/customizer/Readme-generator/page.jsx' --runInBand`, on upstream `b62f481` and on my branch. Before: `page.jsx | 0 | 0 | 0 | 0 | 3-167` with 8 suites and 31 tests passing. After: `page.jsx | 100 | 100 | 100 | 100` with 9 suites and 36 tests passing. To prove the tests can fail, I temporarily removed the badge `trim()` and renamed the download to `readme.txt` in `page.jsx`. The new file reported `1 failed, 4 passed` at the `download="README.md"` assertion, and I then restored the file. Finally, I ran the repo's own CI checks from my ship manifest: `npx jest --runInBand` (CI's `npm test`), `npx jest --coverage --runInBand`, and `npm run format:check` ("All matched files use Prettier code style!"). The husky pre-commit hook ran the same Prettier and test commands on both commits.

**Toolkit**

- Tool: Jest with React Testing Library, run from the reproduction's own coverage command, plus a deliberate break of `page.jsx` to test the tests.
- What it produced: A 0% to 100% coverage change on `page.jsx`, 36 of 36 tests passing, and a failing run (`1 failed, 4 passed`) when the page's download filename was broken.
- Difficulties: The `npm test` scripts use Unix `NODE_OPTIONS=...` syntax that cmd.exe can't run, and jsdom implements neither `URL.createObjectURL` nor anchor navigation.
- What changed: I ran Jest directly with `NODE_OPTIONS` set in PowerShell, ran the pre-commit hook with npm's script shell pointed at Git Bash, and mocked only the two missing browser APIs, restoring them after each test.

---

## Implementation Notes

**2026-11-03**

I claimed Nimbus #973 first, but the repository asks contributors to wait for assignment and no maintainer answered. After checking two more candidates that already had human claims, I switched to WebDevTools #393, posted my claim, and wrote `CLAUDE.local.md` with the Readme generator's files, Jest conventions, and the exact commands.

**2026-11-04**

I reproduced the 0% coverage on `b62f481`, wrote `plan.md`, and got an accept from plan-check after adding a risks section. I wrote five tests against the real page instead of a mocked `NavBar`, because the toggle worked under jsdom, and I read the downloaded Blob with `FileReader` in case jsdom's `Blob` lacks `text()`. My first commit was blocked by the husky pre-commit hook. Prettier flagged 190 files because `core.autocrlf=true` had given them CRLF endings, so I set `core.autocrlf=false` in my clone and checked the files out again. Then `npm test` failed inside the hook because cmd.exe can't parse `NODE_OPTIONS=...`, so I committed with npm's script shell set to Git Bash instead of skipping the hook.

**2026-11-04**

I temporarily broke `page.jsx` (removed the badge trim and renamed the download) to confirm the tests fail: `1 failed, 4 passed`. I restored the file, captured before and after coverage in `test_evidence.md`, and added the "Run the Tests" step to `contributing.md` as a separate commit.

**Toolkit**

- Tool: Claude Code in my WebDevTools clone, guided by `CLAUDE.local.md` and `plan.md`.
- What it produced: Two commits on `test/readme-generator-jest-tests`: `d98e5d5` (178 lines of tests) and `024cbb4` (13 lines of docs).
- Difficulties: The repo's pre-commit hook assumes a Unix shell and LF line endings, so it blocked both commits on Windows before checking anything about my change.
- What changed: I fixed my clone's line-ending setting and npm's script shell rather than using `--no-verify`, and recorded both in `plan.md`'s Deviations section.

---

## Pull Request

**PR Link:** https://github.com/Bashamega/WebDevTools/pull/692

**PR Description:** The PR adds `__tests__/customizer/Readme-generator/page.test.jsx` (five React Testing Library tests for rendering, typing, the preview, the theme toggle, and the README.md download) and a "Run the Tests" step in `contributing.md`, with `Fixes #393`. It pastes the before and after coverage for `page.jsx` (0% to 100%), the failing output from my deliberate break of the download filename, and the Jest and Prettier results, and it says I did not change `page.jsx`, `NavBar`, the Jest config, or dependencies. It fills in the repo's PR template and ends with a short AI-use note.

**Maintainer Feedback:**

- 2026-11-04: CodeRabbit (a bot) rated the merge risk minimal and left one trivial nitpick: add a test that a saved dark theme is restored when the page loads. No human feedback that day.
- 2026-11-05: Bashamega approved the PR without change requests and merged it; issue #393 closed automatically. I left the CodeRabbit suggestion for a possible follow-up rather than adding scope after review.

**Status:** Merged

**Toolkit**

- Tool: ship-gate, run by hand with `check_gate.py --branch` and then as the hook on `gh pr create` through Claude Code, alongside my voice guard.
- What it produced: A PASS with all three manifest checks ok and pr-precheck passing all seven checks. When I opened the PR, the voice guard stopped it twice with my own rules: "If I say something failed, the failing text goes in the comment" and "every 'tested' must point at output."
- Difficulties: The ship-gate hook only watches the Bash tool, so the first attempt through PowerShell skipped it. An inner Claude session also tried to append a "Generated with Claude Code" footer I had decided against. And an edit that merged a code fence with the next sentence broke the posted description's formatting.
- What changed: I pasted the real Jest failure output and the CI check output into `pr_draft.md` and `test_evidence.md`, told the session to run the command with the Bash tool and the body unchanged, and fixed the code fence with `gh pr edit` right after opening the PR.

---

## Learnings and Reflections

**Technical Skills Gained**

I learned to test a React page through what a user can observe: finding fields with `getByLabelText` and buttons with `getByRole` instead of class names, and asserting on the iframe's `srcdoc` rather than component state. I learned how to mock only the browser APIs jsdom lacks (`URL.createObjectURL` and `HTMLAnchorElement.prototype.click`), restore them after each test, and read a `Blob`'s contents with `FileReader`. I also learned to use coverage as reproduction evidence (`--collectCoverageFrom` on one file), and to prove a test is meaningful by breaking the code on purpose and watching it fail.

**Challenges Overcome**

Choosing the issue took several rounds: DNA, Meteor, and other WebDevTools issues already had open PRs, and Nimbus #973 required an assignment that never came. On Windows, the repo's tooling assumed a Unix machine. The husky pre-commit hook failed on 190 files because of CRLF line endings, and `npm test` failed because cmd.exe can't parse `NODE_OPTIONS=...`. I fixed my clone's `core.autocrlf` and ran the hook through Git Bash instead of skipping it with `--no-verify`. My own voice guard also stopped my PR twice until I pasted the actual failure and check output instead of summarizing them.

**What I'd Do Differently Next Time**

I'd check a repo's assignment policy and its open PRs before claiming, instead of discovering a blocked or duplicated issue afterward. I'd run the project's pre-commit hook and CI commands on untouched main before writing any code, so environment problems like line endings show up as baseline facts rather than surprises mid-commit. And I'd write my PR description from `test_evidence.md` from the start, pasting output instead of summarizing it, since that's what both my voice guard and reviewers want.

---

## Resources Used

- [WebDevTools `contributing.md`](https://github.com/Bashamega/WebDevTools/blob/main/contributing.md) and its [`.github/workflows/jest_tests.yml`](https://github.com/Bashamega/WebDevTools/blob/main/.github/workflows/jest_tests.yml): the install steps and the exact CI test commands my manifest copies.
- [Issue #393](https://github.com/Bashamega/WebDevTools/issues/393): the task list (rendering, interactions, state, docs) that set my test scope.
- [Testing Library query priority guide](https://testing-library.com/docs/queries/about/#priority): why I query by label and role instead of class names.
- [Git `core.autocrlf` documentation](https://git-scm.com/docs/git-config#Documentation/git-config.txt-coreautocrlf): explained why Prettier saw CRLF endings on Windows.
- WebDevTools merged PRs [#581](https://github.com/Bashamega/WebDevTools/pull/581), [#452](https://github.com/Bashamega/WebDevTools/pull/452), [#441](https://github.com/Bashamega/WebDevTools/pull/441), [#440](https://github.com/Bashamega/WebDevTools/pull/440), [#423](https://github.com/Bashamega/WebDevTools/pull/423): the review times behind my playbook's thresholds.
