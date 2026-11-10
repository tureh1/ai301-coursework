# Contribution: Test that a saved dark theme is restored on load (follow-up to #692)

**Contribution Number:** 2
**Student:** Tu Reh
**Issue:** https://github.com/Bashamega/WebDevTools/issues/393 (closed by #692; this PR is the follow-up the owner invited on #692)
**Pull Request:** https://github.com/Bashamega/WebDevTools/pull/693

Ungraded record of a second contribution. `contribution-story.md` is the graded one.

---

## Toolkit

### Unit 5: Not used for this round

This follow-up came from a review suggestion on my merged PR, not from a search, so the scout did not run.

### Unit 6: Reused my voice guide and the repo's CLAUDE.local.md

The claim step was the owner's invitation on #692 ("You are welcome you can open a separate pr if you want"), so no new claim comment was needed. My existing `CLAUDE.local.md` for WebDevTools already had the test commands.

### Unit 7: Ship-gate passed on the first full run

I kept the same `ship-manifest.md` from #692 (Jest, Jest with coverage, repo-wide Prettier). The ship-gate passed all three repo checks and all seven pr-precheck checks, and the voice guard passed the description on its first manual check.

### Unit 8: Answered a change request in one reply

On 2026-11-06 the owner requested changes and pointed at CodeRabbit's review. Following my playbook's response rules, I pushed both fixes first, then posted one reply that answered each comment in order with the commit and failing-test output, and named the scope change instead of hiding it.

---

## Why I Chose This Issue

CodeRabbit's review of #692 pointed out that my tests checked the theme toggle but never the restore-on-load path, and I named it in my thank-you. The owner invited a separate PR for it, so it was a small, already-approved piece of work in a repo where I had just had a PR merged.

**Toolkit**

- Tool: none (the work came from the owner's invitation on #692)
- What it produced: n/a
- Difficulties: none met
- What changed: nothing

---

## Understanding the Issue

### Problem Description

No test checks that a dark theme saved in `localStorage` is applied when the Readme generator page loads.

### Expected Behavior

With `theme` saved as `"true"`, the page should load with the dark preview colors, and a test should fail if that stops happening.

### Current Behavior

The restore code works, but nothing tests it: coverage on main shows `navbar.jsx` lines 13-16 uncovered, and line 13 is the restore call.

### Affected Components

`src/components/navbar.jsx` (the mount effect that restores the theme) and `__tests__/customizer/Readme-generator/page.test.jsx` (where the test goes).

**Toolkit**

- Tool: Jest coverage filtered to `navbar.jsx` and `page.jsx`
- What it produced: `navbar.jsx | 75 | 66.66 | 83.33 | 75 | 13-16,28-32`
- Difficulties: none met
- What changed: nothing

---

## Reproduction Process

### Environment Setup

The same Windows 11 clone as #692 (Node.js v24.12.0, npm 11.6.2, `core.autocrlf=false`), synced to upstream main `ce28cbe`, on branch `test/readme-generator-saved-theme`.

### Steps to Reproduce

1. Set `NODE_OPTIONS=--experimental-vm-modules`.
2. Run `npx jest --coverage --collectCoverageFrom='src/components/navbar.jsx' --collectCoverageFrom='src/app/customizer/Readme-generator/page.jsx' --runInBand` on main.
3. Observe `navbar.jsx` lines 13-16 uncovered with 36 tests passing.

### Reproduction Evidence

- **Commit showing reproduction:** none; it used upstream main `ce28cbe` unchanged.
- **Screenshots/logs:** `navbar.jsx | 75 | 66.66 | 83.33 | 75 | 13-16,28-32`; `Tests: 36 passed, 36 total`
- **My findings:** the restore path is untested.

**Toolkit**

- Tool: Jest coverage
- What it produced: the uncovered-lines row above
- Difficulties: none met
- What changed: nothing

---

## Solution Approach

### Analysis

`NavBar`'s mount effect reads `localStorage.getItem("theme")` and calls `toggleTheme()` when the saved value differs from `isDarkMode`. #692's tests only click the toggle, so they never reach that call.

### Proposed Solution

Add one test that saves `theme = "true"`, renders the page, and checks that the preview uses `background-color: #1a202c` and white text. I am not changing `NavBar`, `page.jsx`, the Jest config, or dependencies, and not testing the invalid-JSON `catch` branch.

### Implementation Plan

1. Add the test next to the toggle test.
2. Temporarily disable the restore call and confirm only the new test fails, then restore it.
3. Rerun coverage, the full suite, and Prettier.

No deviations.

**Toolkit**

- Tool: `plan.md` read by pr-precheck inside the ship-gate
- What it produced: `diff-matches-plan [pass]: Single hunk adds exactly the planned test`
- Difficulties: none met
- What changed: nothing

---

## Testing Strategy

Before (main `ce28cbe`): `navbar.jsx` lines 13-16 uncovered, 36 tests. After (`1a10258`): `navbar.jsx | 79.16 | 80 | 83.33 | 79.16 | 16,28-32`, 37 tests passing. With the restore call commented out, the new test failed with `Received string: "...background-color: white; color: black;..."` and `Tests: 1 failed, 5 passed, 6 total`, while the toggle test still passed. The repo checks `npx jest --runInBand`, `npx jest --coverage --runInBand`, and `npm run format:check` all passed.

**Toolkit**

- Tool: Jest, plus a deliberate break of `navbar.jsx`
- What it produced: the before/after coverage and the failing run above
- Difficulties: the husky hook's parallel run printed one "worker process has failed to exit gracefully" warning; it did not recur in three more full runs, and `--detectOpenHandles` found nothing in the test file
- What changed: nothing

---

## Implementation Notes

### 2026-11-05

Synced my fork's main with upstream (which now includes #692), added the test on `test/readme-generator-saved-theme`, proved it fails when the restore call is disabled, and committed `1a10258` through the repo's pre-commit hook.

### 2026-11-05

Ran the ship-gate (PASS), checked the description with the voice guard (pass), pushed, and opened #693 through Claude Code so both hooks checked it.

### 2026-11-10

Review round 1. The fix grew the scope from tests only to one selector in `page.jsx`, so I recorded it as a deviation in `plan.md`. On the first ship-gate run my pr-precheck rejected the branch: `description-matches-diff [fail]: Description opens 'This adds one test...', but the diff adds three tests and a production fix to page.jsx.` I rewrote the opening line, and the next run passed. Suite: 39 of 39 tests.

Round 2, same day. My voice guard stopped the description line "So a dark preview on a light OS showed light code blocks behind white text" under my rule against stating a cause I have not demonstrated, because I had checked the CSS but never rendered it. I reworded it to say what the stylesheet's rules imply and how I checked. Then pr-precheck rejected the branch because the Screenshots section still said "no UI change"; I corrected it. Suite: 40 of 40 tests.

**Toolkit**

- Tool: ship-gate and voice guard
- What it produced: PASS on the first run, 3/3 repo checks and 7/7 pr-precheck checks
- Difficulties: none met
- What changed: nothing

---

## Pull Request

**PR Link:** https://github.com/Bashamega/WebDevTools/pull/693

**PR Description:** One test that a saved dark theme is restored when the Readme generator loads, as a follow-up to #692. It pastes before/after coverage for `navbar.jsx`, the failing output from disabling the restore call, and the Jest and Prettier results, and says no app code changed.

**Maintainer Feedback:**

- 2026-11-05: none yet; Jest and Prettier CI pass, and Vercel is waiting on the owner's authorization as it did on #692.
- 2026-11-06: Bashamega requested changes: "Please check the code rabbit comments." CodeRabbit found that the preview's `body` style rule loses to github-markdown-css's `.markdown-body` colors, so the dark preview could render light, and suggested a saved-light-theme test.
- 2026-11-10: I fetched the stylesheet and confirmed the rule, changed the selector to `body.markdown-body` in `page.jsx` (`97c4548`), added the light-theme and selector tests (`d7a5424`), and showed each fails when its fix is reverted. I pushed, updated the title and description (including the scope change to `page.jsx`), and posted one reply naming both commits with the failing-test output: https://github.com/Bashamega/WebDevTools/pull/693#issuecomment-6777608437

- 2026-11-10: CodeRabbit re-reviewed and left one "Major" comment: the default stylesheet picks code-block colors from the OS color scheme, not the page's toggle. Its summary rated merge risk Low and said this could be a follow-up. Because the owner asked to address CodeRabbit's comments, I fixed it in `ee3122e`: the preview loads the dark or light 5.2.0 stylesheet based on the toggle, and a new test fails with the old single stylesheet. I posted a second reply: https://github.com/Bashamega/WebDevTools/pull/693#issuecomment-6780678396

**Status:** Iterating (waiting on re-review)

**Toolkit**

- Tool: ship-gate hook and voice guard on `gh pr create`
- What it produced: PR #693 opened with no stops
- Difficulties: none met
- What changed: nothing

---

## Learnings and Reflections

### Technical Skills Gained

Using coverage on a shared component (`navbar.jsx`) to find an untested path, and confirming a new test is the only one guarding it by breaking that path.

### Challenges Overcome

None beyond #692's; the Windows setup fixes from #692 carried over.

### What I'd Do Differently Next Time

Add tests for both directions of a stateful feature (save and restore) in the first PR.

---

## Resources Used

- [CodeRabbit's review on #692](https://github.com/Bashamega/WebDevTools/pull/692), which named the missing restore test
- [The owner's reply on #692](https://github.com/Bashamega/WebDevTools/pull/692) inviting the separate PR
