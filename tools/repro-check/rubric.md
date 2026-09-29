# Rubric: is this reproduction package ready to post?

This rubric grades one reproduction package — a candidate claim comment
and a candidate repro report, read against the issue they belong to.
Every check below judges the proof itself: what the artifacts show, what
the words claim, and whether a stranger could get the same result. No
check counts headings, sections, or length, because a terse complete
report is ready and a long confident one can be empty.

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| env-recorded | The repro report's environment record, read against the issue context: the version, OS, and platform the issue is reported on, plus any dependency, build profile, driver, backend, or shell the issue's own text says the behavior turns on. | Pass if the report names the version of the software actually exercised AND every platform-or-runtime fact the issue's text says the behavior depends on (the OS when the issue is OS-specific, the driver, backend, or build profile when the issue names one), so that a reader can place the attempt against the report. A report that states no environment at all fails, even when its artifact looks right. If the environment exercised differs from the one the issue is reported or confirmed on, the report must say so in its own words; an unstated difference fails here. One dense line naming these facts is sufficient — no particular format is required. | required |
| steps-rerunnable | The repro report's steps and commands, read against the trigger the issue describes and against the inputs the package itself supplies. | Pass if a reader holding the environment named in env-recorded could execute the same sequence from the stated starting state and arrive at the trigger, using only inputs the package gives them verbatim or points to publicly (commands, config contents, file contents, a public playground or repository). Fail if a step names an outcome instead of an action ("set the project up", "configure it as usual"), or depends on an artifact the reader cannot obtain — a private repository, an unshared config, an internal file, a local path whose contents are never shown. | required |
| artifact-present | The output the repro report actually shows — command transcripts, log excerpts, produced files, measurements, described screenshots — as distinct from the sentences asserting what happened. | Pass if the package shows at least one artifact produced by its own run that bears on the behavior under discussion. Fail if the report only asserts an outcome with nothing shown, and fail if the only artifacts shown establish something other than that behavior — that the tool is installed, that it launched, that a version banner printed, that a session exists — since those show the run happened, not what it did. | required |
| behavior-matches-issue | The shown artifact compared, detail by detail, against the specific behavior the issue describes: its error text, exit status, symptom, and the exact trigger (syntax, input, flag, configuration) the issue says produces it. | Pass in exactly one of two ways. (a) The report concludes it reproduced, and its artifact exhibits the issue's own symptom — the same failure mode and the same error or observable result — produced by the issue's own trigger. (b) The report concludes it could not reproduce, and its artifact is the result of a genuine attempt at the issue's own trigger. Fail when the artifact shows a different failure mode than the issue reports (a graceful validation error where a crash was reported, a compile error where a runtime error was reported, a surviving program where a termination was reported), or when the run deviated from the issue's trigger — a different syntax, a different input shape, a version other than the one the issue is confirmed on — and the report does not name that deviation and say what it means for the result. | required |
| conclusion-honest | Every assertion of certainty, cause, or scope in the claim comment and the repro report — "reproduced", "cannot reproduce", "the root cause is", "guaranteed", "happens on every build", "confirmed on two machines" — set against what the shown artifacts actually establish. | Pass if each such assertion is either backed by something the package shows, or is explicitly marked as a hypothesis, a suspicion, or a next step to check. A cannot-reproduce is a pass here when the package says plainly that it did not reproduce and names what differed from the report's conditions. Fail when the package asserts a root cause it never demonstrates, states a certainty ("guaranteed reproducible", "definitely a race") no artifact supports, generalizes the result to builds, versions, or platforms it never exercised, or narrates an outcome the shown artifact contradicts. | required |
| claim-specific-and-modest | The candidate claim comment, read against the issue's own content and against what its author can actually control. | Pass if the claim names at least one specific from this issue that only a reader of it could name — its error text, the file or function it points at, its version, its exact trigger, or the state of its thread — AND states a concrete next action the author will take. Fail if the claim would read identically under any other issue ("+1", "same here", "please assign this to me", "great project, I would love to contribute"), or if it promises what its author cannot guarantee: a fix, a delivery date, a guaranteed outcome, or a reservation of the issue. Promising an investigation and a report back is the passing shape. | required |
| repo-conventions | The repo-facts block's stated contribution and AI policy, read as a condition on the two comments being posted. | Pass if the comments satisfy every condition that policy places on comments. Where the policy requires that AI assistance be disclosed, the comments must disclose it, naming the tool and the extent of its use; the packages graded here are AI-assisted work, so saying nothing is non-disclosure and fails. Where the policy requires that comments to maintainers be in the contributor's own words, the comments must read as a person's own account rather than generated filler. Where the stated policy places no condition on comments — no AI policy at all, a permissive "you are responsible for what you submit" policy, or a policy whose disclosure requirement is scoped to pull requests or code rather than to issue comments — this check passes on that ground, and no disclosure is required for it to pass. | required |
| control-or-contrast | A second run in the repro report whose only difference from the failing run is the suspected trigger: the same command with the trigger removed, a working input shown beside the failing one, or a baseline version. | Pass if the package shows such a contrasting run, or says why one is not available for this behavior. This check never changes the verdict; it marks the difference between a package that shows a failure and one that isolates it. | preferred |

## Verdict rule

The verdict is `accept` if and only if every `required` check is graded
`pass`. A single `required` check graded `fail` makes the verdict
`reject`. A `required` check graded `unclear` counts as a fail and makes
the verdict `reject`: proof I cannot verify from the package is proof
that is not ready to post, and the repair belongs to the author
supplying the missing evidence, not to the grader assuming it.

`preferred` checks never change the verdict in either direction. Report
their grades so the author can see what would strengthen the package,
then set them aside when applying the rule above.

On a claim-only draft in live mode, the checks whose evidence is the
repro report — env-recorded, steps-rerunnable, artifact-present,
behavior-matches-issue, and control-or-contrast — are reported
`unclear` with the evidence `not yet applicable: claim-only draft` and
are left out of the rule entirely. The verdict is then decided by
claim-specific-and-modest and repo-conventions alone, and answers only
the narrower question: is this claim comment ready to post? For
conclusion-honest on a claim-only draft, grade only the assertions the
claim comment itself makes; if the claim asserts no outcome, the check
passes.
