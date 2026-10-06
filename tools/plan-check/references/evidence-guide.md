# Evidence guide: where evidence lives in a plan package

Every eval package (for example `calib-01.md`) has the same parts, in
this order: a header with the source, a `## Repo facts` block, an
`## Issue` section, `## Thread highlights`, `## Repro evidence`,
`## Candidate plan`, and `## Candidate plan comment`. Live mode maps
those parts to GitHub and to the student's drafts, as noted under each
family.

## Diagnosis and grounding

- Where it lives: the cause is in the candidate plan, under a
  "Diagnosis" or "Cause" heading or in its first sentences, and often
  restated in the plan comment ("the root cause is ..."). The behavior
  that cause must explain is in `## Repro evidence`: the numbered
  steps, the "Control" lines, and the closing "Actual" paragraph. In
  calib-01 the cause is "that view's model is not refreshed" and the
  evidence is step 4 (re-entering the view fixes the color). Live: the
  cause is in `plan.md`; the evidence is the student's posted repro
  comment as quoted in `plan.md` or the draft comment.
- What good looks like: the stated cause names a code location, and
  every control in the repro fits it. Re-entering the view fixes the
  color, so a missing refresh fits. A diagnosis is ungrounded when a
  control shows the blamed component working under the same conditions
  (the same items parse fine without the flag, the friendly error
  prints from an instance method in the same build), or when a step
  shows the damage before the blamed code runs. Agreeing with the
  thread does not make a diagnosis grounded if the repro contradicts
  it.

## Scope

- Where it lives: the plan's change list ("Change", "Proposed
  changes", "Approach"), its "In scope" / "Not in scope" lines, its
  "Files" list, and the plan comment's sentence describing what will
  be built. Compare them to the issue's title and expected/actual
  lines. Live: the same parts of `plan.md` and the draft comment.
- What good looks like: one change that removes the reproduced
  behavior, with tests, and a not-in-scope line naming the tempting
  bigger work (calib-01: "Out: any change to how push status is
  computed"). Deferring a better long-term rework, or filing a second
  symptom separately, is good scope. A drive-by rewrite looks like a
  list where the actual fix is one item among upgrades, migrations,
  new options or settings, module restructures, cross-component
  unification, retry frameworks, or CI matrices, usually justified as
  "while in the area" or "fix the class, not the instance".

## Executability

- Where it lives: the plan's "Files" list, the named functions or
  code paths in its change or approach, and its ordered steps. Live:
  the same parts of `plan.md`.
- What good looks like: a file or function (calib-01:
  `pkg/gui/controllers/sync_controller.go`, the push completion
  callback) plus one chosen mechanism ("adds the commits context to
  its post-push refresh scope"). A stranger could open that file and
  start. One named unknown inside a chosen area is fine. Unbuildable
  plans read as research agendas: "investigate", "profile", "look
  into", "not sure which layer", "somewhere", "whichever is easier",
  "maybe also", with no file and no chosen mechanism.

## Test plan

- Where it lives: the plan's "Test" or "Test plan" lines, read next to
  the repro evidence's commands and their recorded output. Live: the
  test plan in `plan.md` next to the posted repro steps.
- What good looks like: the repro's own trigger re-run, with the
  result that must change named exactly (calib-01: "at step 3 the
  color must flip without leaving the view"; an exit code, a printed
  value, a log line, a status code). Controls re-run unchanged are a
  plus. A vague test plan says "should work", "should feel fast",
  "nothing else should break", or only "run the full test suite",
  which cannot tell a fixed build from an unfixed one on the reported
  behavior.

## Honesty

- Where it lives: the plan's "Risk", "Unknowns", or "Open question"
  lines; hedges inside the approach ("I have not yet verified which
  layer ..."); every certainty the plan comment asserts ("root cause
  is", "red herring", "should be a small PR"). In live mode, also the
  `## Deviations` section at the end of `plan.md`, which records what
  changed during the build and why.
- What good looks like: unknowns named as unknowns, with what the
  author will do about each ("I will verify the remaining three tools
  against their manpages and note any that fail"). False confidence is
  a cause or a scope claim stated as fact that the repro evidence never
  showed. A deviation that is honest is written in `plan.md`, not left
  for a reviewer to find in the diff.

## Comms

- Where it lives: the plan comment, read against `## Thread
  highlights` (the author's role is in parentheses after the name:
  OWNER, MEMBER, COLLABORATOR, or NONE) and against the `contribution
  policy` line of `## Repo facts`. Live: the draft comment against the
  issue's live thread on GitHub and the repo's CONTRIBUTING.md and any
  AI policy file. Path Review's house rules in `scope.md` also apply
  live: a classmate's plan does not block the student's, and "same
  approach as above" is not a plan.
- What good looks like, thread: where a maintainer named a culprit, a
  direction, a rejected approach, or posted a patch, the comment
  follows it or names it and says why it differs; where someone opened
  a PR for the issue, the comment names it and says how this work
  relates (not racing it, adding tests to it). With no maintainer
  direction and no PR in the thread, a comment that reports the repro
  and the plan is enough. Boilerplate looks like a comment that would
  read the same on any issue, or one that proposes docs while the
  owner has already pointed at the code.
- What good looks like, policy: quote the policy line first and let
  its words set its reach. "All AI usage in any form must be
  disclosed" covers comments, so the comment must say AI was used and
  how. "Comments must be in your own words" needs a specific, personal
  account. "You are responsible for what you submit", "no fully
  AI-generated contributions", a disclosure duty only for pull
  requests, or no AI policy at all place nothing on the comment.
