# Procedure: how this skill grades a plan package

These steps grade a plan someone else wrote; they are not steps for
writing one. Follow them in order. Every check name below is a row in
`rubric.md`; every evidence family is a heading in
`references/evidence-guide.md`.

## Read order

1. Read the issue first: its title, body, stated expected and actual
   behavior, and any cause the reporter proposes. Write down, in one
   line, the single behavior the issue reports. This line is the yard
   stick for scope-bounded later.
2. Read the repro evidence block second, before the plan. For each
   numbered step and each control run, note one line: what was run and
   what it showed. Then mark which steps or controls show the behavior
   *absent* or *already present* at some point (for example "control:
   same items without the flag parse fine", "step 4: zeros already gone
   before any cast"). These marks are what diagnosis-grounded is graded
   against, and reading them before the plan stops the plan's
   confidence from setting what you expect to find.
3. Read the thread highlights third. List every comment by an OWNER,
   MEMBER, or COLLABORATOR that names a culprit, a fix direction, an
   approach that was rejected, or a posted patch or test build, and
   every open pull request or patch for this issue anyone names. If
   there are none, write "no maintainer direction, no open PR".
4. Read the repo-facts block fourth, and copy the contribution-policy
   line verbatim, including any AI policy.
5. Only now read the candidate plan, then the candidate plan comment.
   Read the comment as the maintainer on that thread would see it.
6. In live mode, the issue, thread, and repo facts come from GitHub and
   the repo (see the evidence guide's live-mode locations); the repro
   evidence is the student's posted repro comment as quoted in the
   drafts, and the plan and comment are `plan.md` and the draft comment
   file. Read only what the drafts contain and quote; do not fill gaps
   from other files in the working directory.

## Evidence gathering

1. For diagnosis-grounded: copy the plan's stated cause in one
   sentence, naming the component or code location it blames. Copy the
   cause the plan comment asserts, if it states one. Put next to them
   the step and control notes from Read order step 2.
2. For scope-bounded: list every change the plan commits to building,
   one per line, taken from its change list, approach, files, and from
   the plan comment's own description of what will be built. Mark each
   line "needed for the reported behavior", "test for the fix", or
   "other work". Separately list everything the plan says it defers,
   leaves out, or will file separately; those are not changes.
3. For executable: copy the files, functions, or code paths the plan
   names, and the mechanism it chose (what the code will do
   differently). Copy any phrase that leaves a choice open, such as
   "somewhere", "not sure", "whichever is easier", "investigate",
   "profile", "maybe".
4. For test-decisive: copy the test plan. Next to it, write which repro
   step or command it re-runs and the exact observable result it says
   must appear after the fix.
5. For thread-aware: for each item listed in Read order step 3, find
   the sentence in the plan comment that follows it or names it. Copy
   that sentence, or write "not mentioned".
6. For repo-policy: from the policy line copied in Read order step 4,
   write what it requires of comments, if anything, and whether that
   duty covers issue comments or only pull requests or code. Then copy
   the comment's sentence that meets it, or write "absent".
7. For unknowns-stated: copy the plan's risks or unknowns, or write
   "none stated", and copy any certainty the comment asserts
   ("root cause is", "red herring", "should be a small PR").
8. If a part of the package a family needs is missing entirely (no
   test plan, no thread section, no repro block), record "absent" for
   that family. Do not look for it anywhere else in eval mode.

## Check execution

1. Grade the checks in rubric order: diagnosis-grounded,
   scope-bounded, executable, test-decisive, thread-aware, repo-policy,
   unknowns-stated. Grade every check even after one fails, so the
   author sees every problem at once.
2. Grade each check only from the evidence recorded for it under
   Evidence gathering, applying the rubric's pass condition word for
   word. Re-read the package only if the recorded evidence does not
   settle the pass condition, and then only the part the rubric's
   Evidence column names.
3. diagnosis-grounded: for each control or step marked in Read order
   step 2, ask "if the plan's stated cause were true, would this step
   have come out the way it did?" If any answer is a clear no (the
   step shows the blamed component working, or the damage done before
   it runs), fail and quote that step. If the answer is "possibly, but
   the plan's link is untested or unexplained", that is not a no:
   record it under unknowns-stated instead. A plan that copies the
   issue's or a commenter's diagnosis is still graded against the
   repro steps.
4. scope-bounded: fail if any line from Evidence gathering step 2 is
   marked "other work". Quote that line.
5. executable: pass if both a location and a mechanism were recorded
   and at most one narrow unknown remains; fail and quote the
   open-choice phrase otherwise.
6. test-decisive: pass only if the test re-runs the repro trigger and
   names a specific observable result after the fix.
7. thread-aware: fail if any thread item from Read order step 3 is
   "not mentioned" while the plan goes in a different direction from
   it. Pass if every item is followed or named.
8. repo-policy: fail if the policy requires something of comments that
   the comment does not do. Treat every package as AI-assisted work,
   so a comment silent about AI fails wherever the policy requires
   disclosure in comments.
9. When the evidence for a check was recorded as "absent", grade the
   check `unclear`, unless the rubric's pass condition says what an
   absence means (for example thread-aware passes when the thread has
   no maintainer direction).
10. For every check, write one line of evidence: the quote or fact
    that decided it.

## Verdict assembly

1. Collect the grades of the six `required` checks: diagnosis-grounded,
   scope-bounded, executable, test-decisive, thread-aware, repo-policy.
2. Turn every `unclear` among them into `fail`, as the rubric's verdict
   rule says.
3. If all six are `pass`, the verdict is `accept`. If any is `fail`,
   the verdict is `reject`. unknowns-stated is reported but never
   counted.
4. In the readable summary, list each check with its grade, then name
   the deciding check or checks for a reject and quote the evidence
   line that failed each one. For an accept, say that every required
   check passed.
5. In live mode only, after the verdict, check the plan comment
   against `voice-guide.md` and list any rule it breaks, quoting the
   rule. This never changes the verdict.
6. End the output with the JSON block SKILL.md specifies, one entry per
   check in rubric order, and nothing after it.
