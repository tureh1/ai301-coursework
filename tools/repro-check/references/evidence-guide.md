# Evidence guide: where proof lives in a reproduction package

This is the map the rubric's checks read against. For each family of
proof it says where to look — in an eval bundle and, in live mode, on
GitHub — and what a sufficient piece of that proof looks like when you
find it. Every condition here is meant to be observable: two readers
applying it to the same package should reach the same answer.

A note that governs the whole map: the issue context is the standard.
Nothing in a package is good or bad in the abstract. An environment
record is sufficient against what *this* issue depends on; an artifact
is faithful against what *this* issue reports. Read the issue first,
every time, and read the candidate second.

## Environment

**Where it lives.** In an eval bundle: the repro report, usually its
first line or a line beginning "Environment:", and sometimes inside the
transcript itself (a version banner, a `--version` line, a package
manager name in a prompt). The standard it is measured against lives in
two other places in the same bundle — the issue body and thread
highlights, which name the version and platform the behavior was seen
on, and the repo-facts block, whose bug-report template line names what
that project always asks for (version, config, platform). In live mode:
the environment lives only in the student's draft report; the standard
lives in the issue body on GitHub, the thread's later comments (which
often narrow the platform), and the repository's issue template.

**What good looks like.** The record names the version of the software
actually exercised and how it was installed, plus each runtime fact the
issue's own text makes the behavior turn on — the OS when the issue is
OS-specific, the driver or backend when the issue names one, the shell
when shell behavior is implicated, the build profile when debug and
release differ, the dependency version when the thread points at a
dependency. Test it by asking whether a reader could place the attempt:
could they tell whether their own machine is the same situation or a
different one? A single dense line does this well; a multi-section
table adds nothing. Two failures are common and both are real. A report
with no environment at all cannot be placed, however clean its
transcript. A report whose environment quietly differs from the issue's
target — an older release, a different OS than the one the issue is
confined to — has not recorded its environment so much as hidden it;
the difference must be stated by the author, in which case it is fine
and often useful.

## Steps

**Where it lives.** In an eval bundle: the repro report's steps, whether
numbered, prose, or a shell transcript where the commands are the
steps. Supporting inputs count as part of the steps and live wherever
the report puts them — a config file's contents pasted inline, a
`printf` that creates the fixture, a link to a public playground. The
trigger they must reach is in the issue body's own reproduction
instructions. In live mode: the draft report, read against the issue's
reproduction section on GitHub and any refinement of it in the thread.

**What good looks like.** A stranger with the environment from the
previous family could start where the report says to start, run what
the report says to run, and arrive at the trigger. Each step is an
action they can execute, not an outcome they must arrange: "run
`docker compose up -d db`" is a step, "set up the database" is a
request. Everything the run consumes is either supplied verbatim in the
package or reachable by anyone — a public repo, a public playground
link, a config whose full text is pasted. The decisive question is
obtainability, not length: four commands that stand alone are
followable, and a beautifully structured ten-step report that begins
inside a private monorepo with an unshared config is not, because the
reader cannot take the first step. Steps that skip the issue's actual
trigger — running the tool but never the input that provokes it — fail
here as much as steps that cannot be started.

## Behavior shown

**Where it lives.** In an eval bundle: the fenced blocks in the repro
report — command transcripts, log excerpts, produced output files,
measurements — plus any described screenshot. What they are compared
against is the issue body's own artifact: the error text it quotes, the
exit code it reports, the output it shows, the symptom it describes.
The thread highlights matter too, since maintainers often sharpen the
symptom ("only with the GTK backend", "only on the Store build"). In
live mode: the draft's own pasted output, against the same parts of the
live issue.

**What good looks like.** The artifact is output that the described run
produced, and it exhibits the issue's symptom rather than a neighbor of
it. Two distinctions do most of the work here. First, an artifact must
be *about the behavior*: a version banner, a startup log, a session
list, or a screenshot showing that the application is open proves the
run happened and says nothing about what it did, so a package whose
only evidence is of that kind has shown nothing. Second, an artifact
must be *the same failure*: compare failure mode to failure mode, not
vibe to vibe. A graceful argument-validation error with exit 1 is not
the capacity-overflow panic with exit 101 that the issue reports. A
compile-time error from a modified expression is not the runtime path
error the issue reports. Garbled output from a terminal that is still
running is not the crash the issue reports. In each case the report
reads as confident and the artifact is about something else, and the
only way to catch it is to put the issue's quoted error and the
report's shown error side by side and read them both.

Deviation deserves its own note, because it is the most common quiet
failure. When the run used a different version, a different syntax, or
a different input shape than the issue names, the artifact is evidence
about that other thing. Such a package can still be good — versions
move, and testing the current release against an older report is often
exactly right — but only when the author names the deviation and says
what it means. Named, it is a datum. Unnamed, it is a wrong answer
wearing the costume of a right one.

## Honesty

**Where it lives.** Honesty is not a section; it is the seam between two
things that both live in the package. On one side: every sentence in
the claim comment and the repro report that asserts certainty, cause,
or scope — "reproduced", "cannot reproduce", "the root cause is a
debounce race", "guaranteed reproducible", "confirmed on two machines",
"affects all builds", and the report's own Expected/Actual statements.
On the other side: the artifacts from the family above. Read one
against the other. In live mode the same seam runs through the draft,
with the issue thread as the check on scope claims.

**What good looks like.** Every assertion is either shown or flagged. A
cause the package demonstrates is a finding; a cause the package
suspects is a hypothesis, and saying so costs nothing and keeps the
report true. Scope claims are the sneakiest: a run on one build does
not license "happens on every build", and a package that generalizes to
a platform the maintainers specifically could not reproduce on has
overstated itself by exactly the amount that matters.

An honest cannot-reproduce is a full pass here, and recognizing that is
the point of this family. It looks like this: the report says plainly
that it did not reproduce, shows what it actually ran and got, and
names what differed from the reporter's conditions — the OS, the shell,
the input distribution, the limit it could not force — often with a
hypothesis about which difference matters. That package tells the
maintainer something real: the bug is not universal, and here is the
boundary. Weigh it against its opposite, which is the failure this
family exists to catch: a confident reproduction narrated over an
artifact that shows something else, or an "Actual" line stated
backwards from the output printed directly above it. Certainty is not
evidence, and the amount of it in a report is uncorrelated with the
amount of proof.

## Comms

**Where it lives.** Three places, read together. The candidate claim
comment, read against the issue body it answers. The repo-facts block's
`contribution policy` line, which is the repository's stated rules in
condensed form, including any AI policy and any rule about comments.
The repo-facts block's `bug reports` line, which names what the project
asks contributors to supply. In live mode these are the draft comment,
the repository's CONTRIBUTING.md and any AI_POLICY.md, and the issue
template — and `scope.md`'s house rules, which override the wild-GitHub
reading inside Path Review.

**What good looks like.** A claim comment is specific when it could not
be pasted under a different issue: it names this issue's error, file,
version, trigger, or thread state, and it says what the author will do
next. It is honest when it promises only what the author controls — an
investigation and a report back. The two boilerplate shapes to
recognize are the bare me-too ("+1", "same here", "I am also facing
this") and the assign-me template ("kindly assign this to me, I will
fix it in two days"); the second is worse than the first, because it
promises a delivery date on an unexamined problem and asks the
maintainer to hold the issue on the strength of it.

Policy is read conditionally, and the conditional is the whole skill
here. Do not apply a general norm; apply what this repository actually
states, and apply it only as far as it reaches. The cases, in the order
they come up:

- **No AI policy stated.** Nothing to satisfy. Silence about AI is
  correct, and requiring disclosure anyway is inventing a rule the
  project does not have.
- **A permissive policy** — "AI tools are welcome, you are responsible
  for what you submit", "only submit code you understand" — states a
  responsibility, not a disclosure duty. A package that says nothing
  about AI satisfies it.
- **A disclosure duty scoped to code or pull requests.** Read the scope
  literally. A policy requiring the tool and extent to be stated *in
  the pull request*, especially one that says outright there is no such
  ask for issue comments, is satisfied by comments that do not
  disclose. The scope line is the evidence; quote it.
- **A disclosure duty covering all AI use.** Here disclosure is
  required in the comments themselves, naming the tool and the extent
  of the assistance. Work produced in this course is AI-assisted, so a
  comment that says nothing about AI is not silent-and-therefore-fine;
  it is undisclosed, and it fails however excellent the proof above it
  is. This is the one case where a package that passes every proof
  family is still not ready to post, and it is exactly the case that
  reading the policy line carefully catches.
- **A voice requirement** — "comments to maintainers must be in the
  contributor's own words", "AI-generated comments may be hidden".
  Satisfied by a comment that reads as a person's own account of their
  own run: concrete specifics, plain sentences, no generated filler.
  Assistance with grammar is not a violation where the policy says so.

The general rule under all five: quote the policy line before you grade
against it, and let its own words set how far it reaches. Most packages
have no AI policy, so a disclosure check that fires by default will be
wrong far more often than it is right — and a check that never fires
will miss the one repository whose rules it was written for.
