# Toolkit Portfolio

**Student:** Tu Reh

## Featured Tools

I chose issue-scout, repro-check, ship-gate, and PR monitor because they cover the full contribution path: choosing work, proving the problem, deciding whether a change is ready, and responding after a pull request opens. They also contain the clearest examples of my judgment changing after contact with a real repository. Together, they helped me move from several unavailable candidates to WebDevTools issue #393, produce a tested pull request, and respond to its merge without posting unnecessary messages.

**issue-scout**

**What it is, and the judgment hole I wrote:** issue-scout searches GitHub with queries derived from my fit profile, ranks the returned issues, and grades the highest-ranked candidates. I wrote the search strategy for Python backend and testing work plus small React or TypeScript bugs, and I supplied the judgment in its six-check rubric: maintainer activity, repository status, feasible scope, availability, contribution policy, and newcomer clarity. I also chose the ranking order: query priority, then fewest comments, then most recent update.

**How it evolved, dated:** On 2026-10-20, I replaced a vague “open to other issues” profile with searchable preferences and added three queries plus seven ranking rules. On 2026-11-03, the first live scan returned 299 unique results, and its recency rule pushed new, nearly empty repositories to the top. The scan also accepted an issue that a merged PR had already solved, so I patched the availability check to reject issues whose work is already implemented by a merged PR. That same day I replaced the sandbox-style bounded-scope check with `scope-feasible`, which reports straightforward, moderate, or ambitious work instead of rejecting every issue with a discoverable edge. On 2026-11-04, I recorded another limit after two similar WebDevTools test PRs had been waiting since April: repository activity does not guarantee quick review.

**Evidence:** Direct grading rejected DNA #254, DNA #255, and WebDevTools #402 because open PRs were already doing the work. The replacement grading then produced this short record:

```text
WebDevTools #393: accept; difficulty: moderate
scope-feasible: pass — one interactive React page
Available: pass — no assignee, human claim, or related open PR
WebDevTools #401: accept; difficulty: moderate
WebDevTools #395: accept; difficulty: moderate-to-ambitious
```

That evidence changed my decision: I selected #393 instead of competing with an existing contributor. The choice and the scout changes are recorded in course-repo commits `bb4ff59` on 2026-11-04 and `c83c82e` on 2026-11-04.

**repro-check**

**What it is, and the judgment hole I wrote:** repro-check grades a public claim and a reproduction report against a rubric for real repositories. Its judgment is whether the report gives a rerunnable environment, a precise trigger, evidence that distinguishes the bug from a tooling failure, and a conclusion no stronger than the evidence. I installed the real-repository scope so it would not expect the course sandbox, then used it on my claim and reproduction for issue #393.

**How it evolved, dated:** On 2026-11-03, Windows could not create the installer’s shared symbolic links, so I used the documented pointer-file fallback while keeping one canonical voice guide. On 2026-11-04, my first reproduction draft said more than its output proved and treated other passing suites in the same run as a control. I removed the unsupported adjective, included the transient OneDrive `EPERM` installation failure, and added a separate coverage run for a module that already had tests. That comparison showed that Jest could produce nonzero coverage and that the 0% result belonged specifically to the untested Readme generator.

**Evidence:** The accepted report preserved the commands and this before-state:

```text
page.jsx | 0 | 0 | 0 | 0 | 3-167
Test Suites: 8 passed, 8 total
Tests: 31 passed, 31 total

contrast: sitemapXml.js | 76.92 statements | 62.85 branches
```

After implementation, the same targeted coverage command reported `page.jsx | 100 | 100 | 100 | 100` with 36 tests passing. Pull request #692 includes that before-and-after evidence and was merged on 2026-11-05: https://github.com/Bashamega/WebDevTools/pull/692

**ship-gate**

**What it is, and the judgment hole I wrote:** ship-gate is a pre-pull-request hook and a manual readiness check. I wrote its manifest from WebDevTools’ actual CI workflows: the Jest suite, Jest with coverage, and repo-wide Prettier. Its judgment combines those commands with seven pr-precheck questions about plan alignment, description accuracy, reproduction, reported checks, diff cleanliness, repository standards, and maintainer direction.

**How it evolved, dated:** On 2026-11-04, I rewrote the CI commands in `cmd.exe` form because the repository’s `npm test` uses Unix environment-variable syntax. I initially moved repo-wide Prettier out of the must-pass table after it failed on 190 untouched files. The pre-commit hook then exposed the same problem, and I traced it to CRLF line endings created by `core.autocrlf=true`. After changing only my clone to `core.autocrlf=false`, the complete Prettier check passed, so I restored it to the must-pass table instead of weakening the gate. The first hook attempt also misread the PR body path and blocked; fixing the invocation produced a full pass. On 2026-11-05, the same manifest passed unchanged for follow-up PR #693.

**Evidence:** The 2026-11-04 gate log for #692 records:

```text
verdict: PASS
Jest test suite: ok
Jest coverage run: ok
Prettier, repo-wide: ok
pr-precheck: 7 of 7 checks passed
diff-matches-plan: only the planned test and contributing.md changed
repro-rerun: page.jsx moved from 0% to 100% coverage
```

The 2026-11-05 follow-up run also passed all three repository checks and all seven pr-precheck checks before PR #693 opened. The main contribution’s two implementation commits were `d98e5d5` for the tests and `024cbb4` for the documentation.

**PR monitor**

**What it is, and the judgment hole I wrote:** PR monitor reads the pull request state and drafts a next move, but it does not post automatically. I wrote the playbook that decides when to wait, answer feedback, send one nudge, thank a maintainer, or walk away. I based its timing on five merged outside-contributor PRs whose owner response times were 0, 1, 6, 6, and 1 days, so my rules wait 10 quiet days before one nudge and 45 days before closing the loop and moving on.

**How it evolved, dated:** On 2026-11-04, the monitor labeled #692 `feedback-in` because Vercel and CodeRabbit had commented. Its drafted move correctly said “no action — wait” because my playbook excludes bots, but the mismatch taught me not to trust the state label without reading the proposed move and thread. On 2026-11-05, after the PR merged, its draft hedged about whether issue #393 had closed. I checked the issue, removed the uncertain sentence, and shortened the response to one specific thank-you. The maintainer then invited a separate PR for CodeRabbit’s saved-theme suggestion, which became PR #693.

**Evidence:** The monitor log preserves the state changes:

```text
2026-11-04 — state: feedback-in
move: no action — wait; bots do not count

2026-11-05 — state: merged
move: merged close-the-loop comment
review decision: APPROVED
```

The result is visible in the public record: #692 merged, my single thank-you received a maintainer reply, and follow-up PR #693 contains commit `1a10258`: https://github.com/Bashamega/WebDevTools/pull/693

## Inventory

- **voice guard:** A pre-action hook backed by my voice guide for claims, questions, pings, and PR text. It passed my final claim, then stopped the #692 description twice until I included the exact failing-test and verification output instead of saying only that I had tested it.
- **plan-check:** A rubric-driven review of `plan.md` against the live issue and repository. It accepted the #393 plan after I added explicit risks for `NODE_OPTIONS`, the real `NavBar` under jsdom, and `Blob.text()` support.
- **pr-precheck:** The seven-question readiness grader inside ship-gate. It checked that the diff matched the plan, the PR description matched the diff, the reproduction was rerun, checks were reported, the diff was clean, repository standards were met, and maintainer direction was addressed.
- **`CLAUDE.local.md`:** A local memory file for WebDevTools that records the relevant files, Jest conventions, browser API mocks, Windows-ready commands, and repository rules. I excluded it with `.git/info/exclude`, so it helped later sessions without entering the contribution.
- **Jest and React Testing Library:** The implementation-level test tools. They moved the Readme generator from 0% to 100% targeted coverage, and a deliberate break of the download filename produced `1 failed, 4 passed`, proving the new suite could detect a real regression.

## The Arc

**Beat 1**

Date: 2026-10-20 through 2026-11-03

Friction: My original fit profile was too vague to form useful searches, and the first live scan favored brand-new repositories. It also accepted an open issue that a merged PR had already completed.

Change: I rewrote the profile into concrete Python and React preferences, added three queries and ordered ranking rules, and patched the availability rubric so a merged implementation makes an issue unavailable. I also replaced the strict sandbox scope check with `scope-feasible` and difficulty notes.

Result: The next direct grading rejected three issues with active PRs and kept me from posting a competing claim.

**Beat 2**

Date: 2026-11-03 through 2026-11-04

Friction: Nimbus #973 required assignment and received no maintainer response; two more candidates already had human claim comments. My first #393 reproduction also overstated what one coverage run proved.

Change: I graded three WebDevTools replacements, chose #393, checked every thread before claiming, and revised the reproduction with an exact environment, the OneDrive `EPERM` detail, and a separate nonzero-coverage control.

Result: repro-check accepted the claim and report, and the final implementation raised targeted `page.jsx` coverage from 0% to 100%.

**Beat 3**

Date: 2026-11-04

Friction: The project’s Unix-oriented scripts failed under Windows, Prettier rejected 190 untouched CRLF files, and the first ship-gate hook invocation could not read the PR body path. My voice guard also rejected summary claims that did not include their underlying output.

Change: I translated the CI commands for `cmd.exe`, fixed `core.autocrlf` in my clone, restored Prettier to the must-pass manifest, fixed the hook invocation, and pasted the real failing-test and check excerpts into the PR draft.

Result: ship-gate passed all three repository checks and all seven pr-precheck checks; PR #692 opened with concrete evidence instead of unsupported “tested” claims.

**Beat 4**

Date: 2026-11-04 through 2026-11-05

Friction: Automated comments made the monitor label the PR as if human feedback had arrived, and its post-merge draft was uncertain about whether the issue had closed.

Change: I kept bot activity out of my playbook’s human-response rules, read the thread before acting, verified the closed issue myself, and edited the draft down to one specific thank-you.

Result: The maintainer approved and merged #692, replied to my thank-you, and invited the focused saved-theme follow-up that I opened as PR #693.

## Limitations and What's Next

The toolkit helped me finish and ship a real contribution, but it still depends on my reading at the points where repository context matters most. Its structured checks are strongest when they turn evidence into a pause or a question; they are weaker when they infer availability, review urgency, or human intent from incomplete GitHub metadata. Next I would add two small improvements: an issue-scout review-latency signal based on comparable recent pull requests, and a PR-monitor state rule that separates bot-only activity from human feedback before assigning a label. I would also test every hook on both PowerShell and Git Bash during installation so shell and path errors appear before a live claim or PR command.

**Tool limits.**

issue-scout: It can detect assignees, comments, and related pull requests, but it cannot guarantee that an apparently open issue is still wanted or predict how quickly maintainers will review it.

repro-check: It can reject unsupported claims in a written report, but it cannot know that commands were truly run or distinguish a transient environment failure without the author preserving the raw output.

ship-gate: It is only as complete as its manifest, and its first Windows hook invocation showed that a passing manual run does not prove every shell and body-file path is wired correctly.

PR monitor: Its state label still treats bot comments as feedback, and its drafts can hedge about facts such as issue closure unless I verify the public thread before posting.
