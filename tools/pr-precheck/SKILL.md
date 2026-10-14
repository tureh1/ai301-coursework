---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer one question about one PR package: **is this pull request ready
to submit?** Never answer any other question (whether the fix is
clever, whether the issue is worth fixing, whether you would have
planned it differently), and never grade more than one package in a
run.

A PR package is five artifacts:

1. the PR title,
2. the PR description,
3. the commits and the unified diff they produce,
4. the test evidence (the commands run and their output),
5. the plan the PR claims to implement, including its scope, test
   plan, and any deviation notes.

Read the first four against the plan, and read all five against the
issue the plan belongs to and the repo's stated standards (its PR
template, its contributing rules, and its AI-use policy). The
side-by-side read is the whole job: the diff against the plan, the
evidence against the plan's test plan, the description against the
diff, and the description against the repo's asks.

## Inputs and modes

Decide the mode first, from the request:

- If you were handed a package bundle (a markdown file with `Repo
  facts`, `Issue`, `Thread highlights`, `Plan context`, and
  `Candidate PR` sections), you are in **eval mode**.
- If you were asked to check the student's own draft PR for an issue
  URL, you are in **live mode**.

**Live mode.** Read these inputs, and only these:

| Input | Where it comes from |
|---|---|
| The plan | `plan.md` in the working directory (top folder of the student's fork clone), including its `## Deviations` section |
| The diff | the output of `git diff main...HEAD` (three dots), run from the working copy; that is every committed change the branch makes relative to `main`. Also run `git log --oneline main..HEAD` for the commit list and `git diff main...HEAD --stat` for the changed-file list. Uncommitted changes are not part of the PR: do not grade them, but report them if `git status` shows any outside the draft files |
| The title and description | `pr_draft.md` in the working directory: its first line is the title, everything after it is the description |
| The test evidence | `test_evidence.md` in the working directory |
| The issue and its thread | the issue URL the student gave, read on GitHub (via `gh`, the GitHub REST API with `curl`, or the web page) |
| The repo's stated standards | `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` in the working copy, plus the house rules in this skill's `scope.md` |

A house-chain student (one routed to an instructor-provided issue)
reads the house plan and the house repro pack wherever these
instructions say plan and repro; the same checks grade the same
things. Grade only what those inputs contain. Never fill a gap from
other files in the working directory, from memory, or from what the
student probably meant. If `plan.md`, `pr_draft.md`, or
`test_evidence.md` is missing, grade the checks that need it
`unclear` with the evidence `missing: <file>`.

**Eval mode.** The bundle is the whole world. Every fact comes from
the bundle text. Fetch nothing, run no commands against any
repository, and read no other file except this skill's components.
The bundle's `Repo facts` block stands in for the PR template and
contributing docs, its `Plan context` block stands in for `plan.md`,
and its `Candidate PR` section supplies the title, description,
commits, diff, and test evidence. Eval mode always grades a complete
package: run every check and apply the full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` in this skill directory before reading
anything else.

- If its `Repo:` line still holds the placeholder
  `<ORG>/<PATH-REVIEW-REPO>`, stop without grading. Tell the student
  to fill the `Repo:` line in `scope.md` with their section's Path
  Review repo, and emit nothing else.
- If the issue URL is not in the repo named on the `Repo:` line,
  refuse to grade and say which repo the scope allows.
- Otherwise, apply the house rules listed in `scope.md` as stated
  standards of the repo. They sit alongside the PR template and
  `docs/CONTRIBUTING.md`: the template is always used with real content
  in every section, there is one bounded PR per issue, and the PR comes
  from a `<type>/<issue-number>-<slug>` branch on the student's fork.
  The course's AI-use disclosure is required in every Path Review PR
  description, and the template has no section for it, so expect it
  under Notes for Reviewers.

Never guess a scope. In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, after the verdict is decided, read `voice-guide.md` in
this skill directory and hold the outgoing words, the PR title and the
PR description, against its rules. For every rule the draft breaks,
list it in the readable summary, quoting the rule and the sentence that
breaks it. The voice guide is the student's personal style, not a
standard of the repo, so it never changes any check grade or the
verdict. In eval mode, ignore `voice-guide.md` entirely.

## Component reads

Read these four components from this skill directory before grading:

- `rubric.md` defines the checks (name, evidence, pass condition,
  weight) and the verdict rule. It is the only source of checks: never
  add, drop, rename, or soften one.
- `references/evidence-guide.md` is the map: for every evidence family
  a check names, it says where that evidence lives in a bundle and in
  live mode, and what good looks like.
- `procedure.md` is the operating procedure: the read order, the
  gathering moves, how each check executes, and how grades become the
  verdict. Execute it as written, step by step.

When the procedure is silent on a step you need, do not improvise
around it. Do the narrowest reading of the rubric that settles the
check, and name the gap in the summary under "Procedure gaps", so the
student can fix it.

Refusal rule: if `rubric.md` has no filled row in its checks table, or
`procedure.md` has no written steps under its headings (instruction
comments do not count as content), stop. Say which component is empty
and that this tool cannot grade without a rubric and a procedure. Do
not invent checks at runtime.

## Verdict and output

The verdict is binary: `accept` means ready to submit, and `reject`
means hold. There is no third verdict and no score; put reservations in
the check evidence lines.

Before the JSON block, print a short readable summary: one line per
check with its grade and the deciding fact. For a reject, add the
deciding check and its quote (per `procedure.md`'s verdict assembly).
In live mode, add the voice-guide notes and any procedure gaps.

End the reply with the fenced JSON block below, filled in: one entry
per rubric check in rubric order, valid JSON, and nothing at all after
it. `item` is the PR's URL, or the issue URL when the PR is still a
draft, in live mode, and the bundle id (for example `pkg-07`) in eval
mode.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- **Evidence first.** Never grade a check without naming the fact or
  quote that decided it: a file name from the diff, a line of the
  description, a line of output. "Looks fine" is not evidence.
- **Grade the thing, not the polish.** A terse PR whose diff matches
  its plan and whose evidence is decisive is ready. A confident,
  well-formatted PR whose diff does something the plan never named, or
  whose evidence runs a different path, is not. Never grade length,
  headings, or tone. The description's claims are graded only against
  what the diff and evidence show.
- **The rubric decides.** If a check passes by its stated condition
  but feels wrong, it passes. Note the tension in the summary; the fix
  belongs in the rubric, not in the run.
- **The procedure decides how.** Follow `procedure.md` as written and
  report its gaps instead of inventing steps.
- **Honest shortfalls are not failures.** A PR that does less than
  everything, and says so in its plan's deviation notes or its
  description, is graded on what it claims. Holding it for the
  shortfall alone is a grading error.
- **Unclear is a fail.** Apply `rubric.md`'s verdict rule to `unclear`
  grades. Where it is silent, a required check graded `unclear` counts
  as `fail`: a PR you cannot verify from the package is not ready to
  submit.
