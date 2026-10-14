# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/180

**Branch**

fix/61-health-probe-text

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Three runs, in order, all graded with my installed `~/.claude/skills/pr-precheck/`.

1. **5/7**, a partial run with `--only pkg-01,pkg-02,pkg-13,pkg-16,pkg-18,pkg-19,pkg-20`. I chose
   the packages most likely to break a first draft:
   - `pkg-01` and `pkg-20` are the whole `standards-wall` category, which the floor depends on.
   - `pkg-13` and `pkg-16` are the "honestly disclosed shortfall" accepts.
   - `pkg-02` (a diff with identical lines re-printed inside a real hunk) and `pkg-19` (a terse
     description) are accepts that a strict cleanliness or description check could reject.
   - `pkg-18` is the arguable `unreviewable` reject.

   Both standards-wall packages agreed. The two misses were `pkg-13` and `pkg-19`, both gold
   accepts, and both failed `repro-rerun` for the same reason. The grader treated the plan's
   *controls* as failure modes that needed transcripts: "only asserts 'Controls ... unchanged'
   with no command or output, despite the plan's test plan requiring both controls to be
   re-run."
2. **5/5 scored**, a partial run with
   `--only pkg-13,pkg-19,pkg-07,pkg-14,pkg-04,calib-04 --include-calibration`, after I separated
   controls from failure modes in `repro-rerun`. `pkg-13` and `pkg-19` flipped to accept.
   Because the change loosened a not-tested check, I added three `not-tested` canaries (`pkg-04`,
   `pkg-07`, `pkg-14`), and all three stayed reject. I also added `calib-04` as an unscored trap.
   It is the package where the plan names two real failure modes and the PR re-runs only one,
   and it stayed reject for exactly that reason: "repro 2 (-j 200000 -x echo .) is never re-run
   or shown."
3. **19/20**, the confirming full run with `--save-run eval-run.txt`:
   `categories: clear-accept 6/7  not-tested 4/4  silent-drift 4/4  standards-wall 2/2  unreviewable 3/3`.

The committed `eval-run.txt` reports:

`agreement: 19/20 scored items  (bar: 18/20: PASS)`

**Package analysis**

`pkg-05` (nushell/nushell#18848). My tool decided **reject**, and the gold label is **accept**.
This is the one package my final run got wrong, and the reason is a deliberate line in my
`repro-rerun` check.

The plan's test plan names two things to re-run: "re-run the issue's script, expect both
`atuin` rows; re-run with two same-name same-key bindings, expect one row plus a warning." The
PR shows the first one well. The before table has a single `atuin │ none │ up` row, and the after
table has both `atuin │ control │ char_r` and `atuin │ none │ up`. For the second one, the test
evidence only says "Same-key redefine prints the one-time warning." The diff does add a unit test
for it, `same_name_same_key_replaces_with_warning`, which asserts one merged row and
`warnings.len() == 1`. But the only result shown is the whole suite: "`cargo test -p
nu-protocol` passes (312 tests)".

My tool failed `repro-rerun` with this evidence: "the plan's second trigger ('two same-name
same-key bindings, expect one row plus a warning') is backed only by the unproven sentence
'Same-key redefine prints the one-time warning' with no transcript." That is my rubric applied
as written. The second case is a real failure mode, not a control, so it needs output. And my
rule for added tests is "A test the diff adds counts only when it exercises the reproduced
trigger, and its result is shown." The test exercises the right trigger, but its own result
appears only inside a passing suite count.

The gold label reads it the other way, and I think the gold is right on the substance: the
regression test asserts exactly the expected-after the plan names, and a green suite that
contains that test is reasonable proof. Every other check passed `pkg-05`. That includes
`thread-engaged`, where the tool quoted the description answering both maintainers: "Description
directly answers fdncred's uniqueness question and kronberger-droid's warn-and-merge caveat."
I kept the strict reading anyway, for the reason under Trade-offs.

**Check rationale**

The `repro-rerun` row from `tools/pr-precheck/rubric.md`, exactly as it reads now:

> | repro-rerun | The test evidence (and any before/after in the description), read against the plan's test plan and the reproduction it builds on: the exact trigger (command, input, file shape, flag, or scenario) and the expected-after result the plan names. | Pass if the evidence re-runs the plan's own reproduction trigger on the changed code and shows the observable after-result the plan expects (an output, value, exit status, rendered state, or log line), with the before stated or shown beside it. Where the plan's test plan names more than one failure mode or repro of the bug, each one must be re-run with its output shown. Controls (runs the plan expects to stay unchanged, such as the no-trigger case or a reference tool) are not failure modes: a plain statement that a control is unchanged is enough for them, and no output is required. A test the diff adds counts only when it exercises the reproduced trigger, and its result is shown. Fail if the only evidence is an assertion ("tested locally", "verified working on my machine", "works now"), or if the evidence exercises a different path than the reproduction (a control, the single-file case when the bug is cross-file, a different method or input shape) instead of the trigger itself. | required |

It reads that way because the four `not-tested` rejects fail in two different ways, and one
check has to catch both:

- **Assertion instead of proof.** `pkg-04` ("tested locally, colors work now") and `pkg-10`
  ("Verified working on my machine: a full day of WebDAV syncs") show no output at all. So the
  fail clause names those phrases.
- **The wrong path, proven well.** `pkg-07` re-runs "a single-file case" when the bug is
  cross-file, and `pkg-14` re-runs "a GET request carrying one custom header" when the bug is a
  single-header POST. Both have real transcripts, so a check that only asked "is there output?"
  would pass them. The phrase "the plan's own reproduction trigger" and the examples "the
  single-file case when the bug is cross-file, a different method or input shape" are there so
  the grader compares the command to the plan's trigger, not just checks that output exists.

The sentence about controls is the revision, and it came from run 1. My first version said
"Where the plan's test plan names more than one failure mode or repro, each one must be re-run."
The grader counted controls as repros and failed `pkg-13` and `pkg-19`. In `pkg-13` the plan
names "the no-ampersand control unchanged" and the PR states "No-ampersand control unchanged."
in one line, which the gold label accepts. A control isn't the bug, so a statement is enough. I
narrowed "repro" to "repro of the bug", added the explicit control sentence, and mirrored it in
`procedure.md` ("Never fail this check over a control: a stated 'control unchanged' is enough
for controls") so the rubric and procedure can't be read two ways.

**Trade-offs**

The cost of `repro-rerun` is `pkg-05`, a gold accept that my final run rejects (see Package
analysis). The clause that fails it is "A test the diff adds counts only when it exercises the
reproduced trigger, and its result is shown." I could have loosened it to "an added test that
exercises the trigger counts if the suite passes", and that would likely flip `pkg-05` to agree
and make the run 20/20.

I didn't, because that wording would also open the door for `pkg-07`. Its added test,
`TestGroupEditsByFileBuildsGroups`, is the same kind of evidence (a new unit test inside a
passing `go test ./pkg/goanalysis/...`), and the only thing separating it from `pkg-05` is that
it tests same-file grouping, not the cross-file trigger. In run 2 my tool failed `pkg-07` partly
on "the added unit test only groups same-file edits". That depends on the grader reading the test
closely, and "the suite passed" is exactly the shortcut that skips the close read. Since 19/20
already clears the bar, I kept the stricter rule. One arguable accept is a cheaper miss than a
check that waves through tests of the wrong path.

What I accept it will miss: a PR whose only proof of a second failure mode is a well-targeted
unit test inside a green suite. My tool will hold it until the author pastes that test's own
line. On my own PR this cost nothing, because `test_evidence.md` shows the new test failing on
`main`'s `health.py` and passing on the branch, by name.

Run 2 is my evidence that the loosening in my rationale changed nothing else: the three
`not-tested` canaries and `calib-04` all stayed reject after I relaxed the control rule, and
the full run then held `not-tested` at 4/4.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
