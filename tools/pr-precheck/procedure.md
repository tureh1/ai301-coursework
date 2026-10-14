# Procedure: how this tool grades a PR package

These steps grade a pull request someone else wrote; they are not steps
for writing one. Follow them in order. Every check name below is a row
in `rubric.md`; every evidence family is a heading in
`references/evidence-guide.md`.

## Read order

1. Read the repo's stated standards first. In eval mode, copy the
   `Repo facts` block's `pull requests:` line and `contribution policy`
   line verbatim. In live mode, read `.github/PULL_REQUEST_TEMPLATE.md`
   (list its section headings and checklist lines), `docs/CONTRIBUTING.md`
   (note any required commands, entries, or conventions), and the house
   rules in `scope.md`. Write down every ask as a list: "section X",
   "issue reference in form Y", "entry in file Z", "run command C",
   "AI disclosure". These asks are what standards-met and
   checks-reported grade against later.
2. Read the issue and its thread second. Write one line naming the
   single behavior the issue reports. List each comment by an OWNER,
   MEMBER, COLLABORATOR, or CONTRIBUTOR that gives explicit direction
   (a fix shape, a scope decision, a question to the contributor). If
   none, write "no maintainer direction".
3. Read the plan third, before any part of the PR. From the plan (the
   eval bundle's `Plan context` block, or live `plan.md` including its
   `## Deviations` section) write down:
   - the planned change, in one sentence;
   - every file or site the plan names;
   - every deliverable the plan commits to (fix sites, tests, docs,
     entries), one per line;
   - everything the plan says is not in scope, deferred, or changed in
     a deviation note;
   - the test plan's trigger (the exact command, input, or scenario of
     the reproduction) and its expected-after result, one line per
     failure mode of the bug it names. List the plan's controls (runs
     expected to stay unchanged) separately; they are not triggers;
   - any suite or check command the test plan names.
   The plan comes before the diff because the diff is graded against
   the plan, not against the description. Reading the description first
   lets its fidelity claims set what you expect to find.
4. Read the diff fourth: every hunk, file by file. In live mode, this
   is `git diff main...HEAD`, with `git diff main...HEAD --stat` and
   `git log --oneline main..HEAD` beside it. Do not read the
   description yet.
5. Read the test evidence fifth (the bundle's `Test evidence` section,
   or live `test_evidence.md`).
6. Read the title and description last (the bundle's `Title` and
   `Description`, or live `pr_draft.md`: first line is the title).

## Evidence gathering

1. **Diff against plan (diff-matches-plan).** Make a table with one
   row per hunk: file, what the hunk does in a few words, and which
   plan item it serves. Use one of four labels: "planned change", "test
   for it", "required entry" (only if Read order step 1 lists that
   entry as an ask), or "not in plan". For every "not in plan" row,
   search the plan's deviation notes and the description for a sentence
   that explains it, and copy that sentence or write "unexplained".
   Then take the plan's deliverables list from Read order step 3 and
   mark each one "in diff" (name the hunk), "disclosed absent" (copy
   the note), or "missing, undisclosed".
2. **Description against diff (description-matches-diff).** List
   every factual claim the title and description make about the change:
   files, entries, tests, behaviors, and limits ("exactly as planned",
   "no functional changes outside", "only X"). For each, find the hunk
   that proves or breaks it, using the table from step 1, and mark it
   "true", "false: <hunk>", or "not checkable from the diff" (for
   example a claim about runtime behavior, which belongs to the
   evidence).
3. **Evidence against test plan (repro-rerun).** For each trigger line
   from Read order step 3, find the evidence block that runs it. Copy
   the command and the after output. Mark whether the command is the
   plan's trigger itself or a different path (a control, a simpler
   case, another input), and whether the after-result is the one the
   plan expects. Copy the before (shown or stated) beside it. If a
   trigger has no matching block, write "not re-run". If the evidence is
   only a sentence with no output, copy the sentence.
4. **Checks against asks (checks-reported).** Combine the suite and
   check commands from the plan's test plan with the commands in the
   repo's asks. For each, copy the evidence line reporting it and its
   result, or write "not reported". If the list is empty, write "no
   suite or check named".
5. **Debris (diff-is-clean).** Scan every added (`+`) line of the diff
   and copy each line that is a debug print or log, commented-out code,
   a dead or unused function or variable, a TODO or FIXME, or an import
   or formatting change the fix does not need. Mark every hunk whose
   `-` and `+` lines are identical apart from whitespace. Copy the
   commit messages.
6. **Standards (standards-met).** For each ask from Read order step 1,
   copy the description text or diff hunk that honors it, or write
   "absent" or "placeholder only". For the AI policy, write what it
   requires of pull requests (disclosure? own words? nothing?), then
   copy the description's disclosure sentence or write "absent".
7. **Thread (thread-engaged).** For each maintainer direction from Read
   order step 2, copy the description sentence that follows or answers
   it, or write "not mentioned".
8. If a whole part of the package is missing (no test evidence section,
   no description, no plan), record "absent" for every family that
   needs it. In eval mode, never look outside the bundle for it.

## Check execution

1. Grade the checks in rubric order: diff-matches-plan,
   description-matches-diff, repro-rerun, checks-reported,
   diff-is-clean, standards-met, thread-engaged. Grade every check even
   after one fails, so the author sees every problem at once.
2. Grade each check only from what Evidence gathering recorded for it,
   applying the rubric's pass condition word for word. Re-read the
   package only when the recorded evidence does not settle the pass
   condition, and then only the part the rubric's Evidence column names.
3. diff-matches-plan: fail if any hunk row is "not in plan" and
   "unexplained", or any deliverable is "missing, undisclosed". Quote
   the hunk or the missing deliverable. Otherwise pass.
4. description-matches-diff: fail if any claim is marked "false";
   quote the claim and the hunk that breaks it. Claims marked "not
   checkable from the diff" do not fail this check.
5. repro-rerun: fail if any trigger is "not re-run", if the only block
   for a trigger runs a different path, or if the evidence for a
   trigger is a sentence with no output. Quote what was run instead of
   the trigger. Pass when every trigger shows its expected after-result
   and a before. Never fail this check over a control: a stated
   "control unchanged" is enough for controls.
6. checks-reported: fail if any named suite or check is "not
   reported", or if the only report is an unnamed "all tests pass".
   Pass if every one is reported with a result, or if none is named.
7. diff-is-clean: fail if step 5 of Evidence gathering copied any
   debris line or marked any formatting-only hunk; quote the first one.
   Identical lines inside a hunk that also makes a real change are not
   debris.
8. standards-met: fail if any ask is "absent" or "placeholder only", or
   the AI policy requires disclosure and the disclosure is "absent".
   Treat every package as AI-assisted work.
9. thread-engaged: fail if any maintainer direction is "not
   mentioned"; pass otherwise, including when there is none.
10. When the evidence a check needs was recorded as "absent" (Evidence
    gathering step 8), grade it `unclear`, unless the rubric's pass
    condition says what absence means (checks-reported and
    thread-engaged pass when nothing is named or no direction exists).
11. For every check, write one evidence line: the quote or fact that
    decided it.

## Verdict assembly

1. Collect the grades of the six `required` checks: diff-matches-plan,
   description-matches-diff, repro-rerun, checks-reported,
   diff-is-clean, standards-met.
2. Turn every `unclear` among them into `fail`, as the rubric's verdict
   rule says.
3. If all six are `pass`, the verdict is `accept`. If any is `fail`,
   the verdict is `reject`. thread-engaged is reported but never
   counted.
4. For a reject, the deciding check is the first failing required check
   in rubric order. Quote its evidence line in the summary as the reason
   for the hold, then list any other failing checks after it.
5. For an accept, say that every required check passed, and name any
   disclosed shortfall the PR carries, so the reader knows it was seen
   and accepted.
6. In live mode only, after the verdict, apply the voice seam from
   SKILL.md and list any voice-guide rule the title or description
   breaks. This never changes the verdict.
7. End the output with the JSON block SKILL.md specifies: one entry per
   check in rubric order, then the verdict, and nothing after it.
