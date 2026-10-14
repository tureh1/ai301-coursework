# Evidence guide: where evidence lives in a PR package

Every eval package (for example `calib-01.md`) has the same parts, in
this order: a header with the source, `## Repo facts`, `## Issue`,
`## Thread highlights`, `## Plan context`, and `## Candidate PR` with
`### Title`, `### Description`, `### Commits`, `### Diff`, and
`### Test evidence`. In live mode the same evidence lives in the top
folder of the student's fork clone and on GitHub:

| Bundle part | Live-mode location |
|---|---|
| Plan context | `plan.md`, including its `## Deviations` section |
| Diff and commits | `git diff main...HEAD`, `git diff main...HEAD --stat`, `git log --oneline main..HEAD` |
| Title and description | `pr_draft.md` (first line is the title) |
| Test evidence | `test_evidence.md` |
| Repo facts | `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, `scope.md` house rules |
| Issue and thread highlights | the issue page and its comments on GitHub |

## Plan fidelity (harness category: silent-drift)

- **Where it lives.** The plan's scope pair is in `Plan context`: the
  sentence starting "Scope, one bounded change", the "Not in scope"
  line, the "Files:" list, and any "deferred" or deviation note. In
  calib-01, the scope is "Remove the first, shadowed
  `file-decoration-style` line... Not in scope: any other theme.
  Files: `themes.gitconfig`". What actually changed is in `### Diff`:
  each `--- a/<file>` header and its `@@` hunks. The description's
  fidelity claims are in `### Description` ("implements the plan
  exactly", "no functional changes outside..."). Live: `plan.md`'s
  scope, files, and `## Deviations` against `git diff main...HEAD`,
  and `pr_draft.md`'s claims against that same diff.
- **What good looks like.** Every changed file is one the plan names
  or a test for the planned change. Every hunk does the planned change
  (calib-01 deletes exactly the one `#434C5E` line). Every deliverable
  the plan names is in the diff, or the PR says why not. An honest
  deviation re-ties a mismatch: pkg-13's description says
  "percent-sign environment expansion (`%VAR%`) is NOT handled here",
  matching the plan's deferral note, so the shortfall is disclosed, not
  drift.
- **What silent drift looks like.** In the "more" direction: a hunk
  adding a new flag, option, or setting, a renaming pass, or a rewrite
  of a file the plan never touches, with no note (a new
  `time_format` flag under "exactly as planned"). In the "less"
  direction: a planned deliverable such as a docs update missing from
  the diff while the description says it was done. A description that
  claims more or less than the diff delivers is silent drift, not a
  comms problem.

## Test evidence (harness category: not-tested)

- **Where it lives.** `### Test evidence`, plus any before/after the
  description carries. Read it against the plan's "Test plan:"
  sentence in `Plan context`, which names the trigger (calib-01:
  "re-run the configparser repro, expect a clean parse") and the
  repro evidence that trigger came from. The repo's own checks are
  named in the plan's test plan and in `Repo facts`' `pull requests:`
  line (pandas: "tests added and passed, all code checks passed").
  Live: `test_evidence.md` against `plan.md`'s Test plan and the
  checks the PR template lists (`make test-unit`,
  `make test-integration`, `make lint`, `make typecheck`).
- **What decisive looks like.** The plan's own trigger re-run on the
  branch, with the before and the after side by side and the
  after-result the plan predicted (calib-01: the `configparser`
  command, "before: DuplicateOptionError", "after: parsed OK"). Each
  failure mode the test plan names gets its own re-run. Each named
  suite is reported with its command and result (`cargo test -p
  ignore` passes (214 tests)). An honestly reported failure, with its
  reason, still counts as reported.
- **What not tested looks like.** "Verified working on my machine",
  "colors work now", or "all tests pass" with nothing named. Evidence
  that runs the control or a different path instead of the trigger: the
  single-file case when the bug is cross-file, a GET with one header
  when the bug is a POST with one header. A test plan naming two repros
  where only one is re-run.

## Diff quality (harness category: unreviewable)

- **Where it lives.** The `+` lines of every hunk in `### Diff`, and
  the messages in `### Commits`. Live: `git diff main...HEAD` and
  `git log --oneline main..HEAD`.
- **What reviewable looks like.** Each hunk visibly does the fix, its
  test, or a required entry. Comments that explain why the fix is
  shaped this way are fine (pkg-19's "No indentation reset here: it
  dedented the continuation..."). A few unchanged lines re-printed
  inside a hunk that also changes something real are a diff-tool
  artifact, not debris.
- **The debris tells.** A debug print or log line, active or
  commented out (`// eprintln!("help colors: ...")`). Commented-out
  first attempts. A dead or unused helper (`_unused_buffer_probe`,
  `count_open_fds` behind `allow(dead_code)`). A TODO left on an
  import. An import restructure or re-indent the fix does not need. A
  hunk whose `-` and `+` lines are identical, which is pure churn.
  Commit messages like "wip", "fmt + cleanup", or "misc cleanups while
  debugging" point at where the debris is.

## Standards and comms (harness category: standards-wall)

- **Where it lives.** The repo's asks are in `Repo facts`: the
  `pull requests:` line (template sections, checklist items, the
  issue-reference form such as `closes #xxxx` or `fixes #<n>`,
  required entries such as a CHANGELOG or `whatsnew` file) and the
  `contribution policy` line (AI-use rules). Where the PR honors them:
  the `### Description` for sections, checklist answers, the issue
  reference, and the disclosure; the `### Diff` for required file
  entries. Maintainer direction is in `## Thread highlights` (role in
  parentheses after the name). Live: the section headings and checklist
  in `.github/PULL_REQUEST_TEMPLATE.md` (Path Review's are Summary,
  Issue with `Closes #`, Changes, Testing checklist, Screenshots / Demo,
  Notes for Reviewers), `docs/CONTRIBUTING.md`'s conventions, and
  `scope.md`'s house rules, which make the course's AI-use disclosure a
  stated ask for every Path Review PR.
- **What compliant looks like.** Every section the template asks for
  is present with real content. Required checklist items are answered
  truthfully. The issue is referenced the way the repo asks. Required
  entries exist in the diff (pkg-02's CHANGELOG hunk). Where the policy
  demands disclosure, the description names the tool or the fact of AI
  help and how far it went (pkg-11: "this change was AI-assisted with me
  reviewing and understanding every line, and this text is in my own
  words"). Quote the policy line first and let its words set how far it
  reaches: "All AI usage in any form must be disclosed" covers PR text,
  while a repo with no AI policy asks for none.
- **What a wall looks like.** A required checklist skipped entirely
  (no `closes #`, no checklist, and the whatsnew entry missing from the
  diff). A template section left as its placeholder. A strict-AI-policy
  repo whose description never mentions AI. Every package is treated as
  AI-assisted work, so silence is non-disclosure.
