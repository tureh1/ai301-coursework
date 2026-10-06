# Voice guide: how I talk upstream

## Who I am in threads

I am a student making my first open-source contributions, with backend
experience in FastAPI, SQLAlchemy, and PostgreSQL and very little
experience in the social half of a repository I did not write. When I
comment on an issue I am reporting what I ran and what I saw, not
representing an opinion about the project. What a reader can expect
from me is that every factual sentence I post is something I actually
observed on my own machine, and that anything I am guessing at is
labelled as a guess.

## Rules I write by

### Rule: Promise the investigation, never the fix

When I claim an issue I say what I am going to look at and when I will
report back. I do not promise a fix, a pull request, or a date, because
I do not yet know what the bug is; the moment I name a deadline I have
put a maintainer's plan on a guess of mine.

- Wrong: "I'll take this one and have a PR up by the weekend."
- Right: "I'd like to take this one. Next I'm going to reproduce it
  locally against SQLAlchemy 2.x and post what I find; I'll report back
  either way before I open anything."

### Rule: Post the output, not my summary of the output

If I say something failed, the failing text goes in the comment. My
paraphrase of an error is a second-hand account of evidence I already
have in my terminal, and pasting it costs me nothing and saves the
reader from trusting me.

- Wrong: "Confirmed, the health endpoint errors out with a SQLAlchemy
  error."
- Right: "Confirmed on 2.0.32; `GET /health` returns 500 and the log
  shows: `sqlalchemy.exc.ArgumentError: Textual SQL expression 'SELECT
  1' should be explicitly declared as text('SELECT 1')`."

### Rule: Separate what I saw from what I think it means

The observation and the explanation are two different sentences, and
the explanation gets a hedge unless I have proved it. I reach for
confident causal language when I want to sound competent, which is
exactly when I am least sure.

- Wrong: "This is happening because SQLAlchemy 2.0 removed implicit
  string coercion in `Session.execute`."
- Right: "The traceback points at the raw string passed to
  `Session.execute` in `api/routes/health.py`. My guess is the 2.0
  removal of implicit string coercion, but I haven't confirmed that
  against the 1.4 behavior yet."

### Rule: Name my differences before someone else finds them

If my OS, version, or setup is not the one the issue was reported on, I
say so in the same breath as the result. A result from a different
environment is still useful; a result from a different environment
presented as identical is a small lie that wastes the maintainer's
next hour.

- Wrong: "Reproduced, same error."
- Right: "Reproduced, though on Windows 11 with Docker Desktop rather
  than the Linux setup in the report — same `ArgumentError`, so it
  doesn't look platform-specific."

### Rule: Ask for the thing I actually need

When I am stuck I ask one specific question instead of signalling
distress and hoping someone volunteers. Vague appeals put the work of
figuring out what I need onto the person I am asking.

- Wrong: "I'm having trouble getting this set up, any help would be
  appreciated!"
- Right: "`make dev` fails at the alembic step with `target database is
  not up to date` on a fresh clone. Is there a migration step before
  it that isn't in the README, or should the database be empty first?"

### Rule: State the approach as a plan, and say what it leaves out

A plan comment commits me to a change in front of the people who
maintain the code. I say what I will change, where, and what I am
deliberately not touching, and I name anything in the thread or
another issue that overlaps, so nobody has to guess whether my PR will
grow. Where I am not sure the approach is the one the maintainers
want, I say so and invite a different direction before I build.

- Wrong: "I'll clean up the health checks and fix this."
- Right: "Plan: wrap the probe in `text()` in `api/routes/health.py`
  and add a test for the postgres branch. Not touching the Redis
  failure in the same response; that's #62."

## Things I never post

- A date, an ETA, or a guaranteed outcome. I do not know how long a bug
  I have not diagnosed will take.
- "Any update on this?" or "+1" with nothing else. If I have nothing to
  add, the thread is better without my comment.
- A root cause I have not demonstrated, stated as fact.
- "Same as above, can confirm." If I reproduced it, I post my own
  environment, my own steps, and my own output; if I did not, I say
  nothing.
- Flattery as a lead-in. "Great project, I love this repo" is a way of
  asking for goodwill before I have done anything, and every maintainer
  has read it a thousand times.
- A request to have the issue reserved or assigned to me before I have
  shown any work on it.
- Undisclosed AI assistance in a repository whose stated policy asks
  for disclosure. If the policy asks, I name the tool and what it did,
  in the comment itself.
