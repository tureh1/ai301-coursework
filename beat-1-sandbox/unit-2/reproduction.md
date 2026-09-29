# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

tureh1

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5895951670

I'd like to take this one as a first contribution.

In `api/routes/health.py` the postgres branch calls `await db.execute("SELECT 1")` on the session from `get_db()`. SQLAlchemy 2.x no longer coerces a bare string into textual SQL, so that call raises and the `except` block sets `"postgres": "unhealthy"` in the response even when the database is reachable, which matches the `ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')` in the issue body.

Next I'm going to stand the stack up from `docs/SETUP.md` on Windows 11 with Docker Desktop, call `GET /health` against `main` at `f89c06f`, and post back the environment, the exact request, and the unedited error output. I'll report what I find either way, including if it turns out not to reproduce on my setup.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5896400922

Reproduced on `main` at `f89c06f`.

**Environment:** Windows 11 Pro (26200), Git Bash; Docker Desktop 29.1.3 with `postgres:16-alpine` (server reports PostgreSQL 16.14); Python 3.12.10, SQLAlchemy 2.1.1, asyncpg 0.31.0, FastAPI 0.141.1, uvicorn 0.54.0. The issue does not name a platform, so this is a Windows data point rather than a match to the reporter's setup.

**Steps**

```bash
git clone https://github.com/codepath/pathreview-ai301-fa26-s1.git
cd pathreview-ai301-fa26-s1          # f89c06f
cp .env.example .env                 # unedited; LLM_PROVIDER=mock needs no key
docker compose up -d
python -m venv .venv
.venv/Scripts/python -m pip install -e ".[dev]"
.venv/Scripts/alembic upgrade head
.venv/Scripts/python -m uvicorn api.main:app --host 127.0.0.1 --port 8000
```

Then, from a second shell:

```bash
$ curl -s -o /dev/null -w "HTTP %{http_code}\n" http://127.0.0.1:8000/health
HTTP 503
```

I ran `uvicorn` directly rather than `make run`, and skipped the frontend install and `scripts/seed_db.py`, since `/health` touches neither.

**Observed**

```json
{
  "detail": {
    "status": "unhealthy",
    "dependencies": {
      "postgres": "unhealthy",
      "redis": "unhealthy",
      "vector_db": "healthy"
    },
    "safety_events_last_hour": 0,
    "timestamp": "2026-09-29T17:58:37.339325"
  }
}
```

The server log gives the reason, and it is the `ArgumentError` from the issue body:

```
2026-09-29 12:58:37 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')" request_id=e032e7bb-8ff5-4625-a2eb-56b1413a7d98
```

**Control — the database is up; the bare string is what fails**

Same `get_db()` session, both statements in one run, so the only difference is `text()`:

```python
import asyncio
from sqlalchemy import text
from core.database import get_db

async def main():
    async for s in get_db():
        print("text('SELECT 1') ->", (await s.execute(text("SELECT 1"))).scalar())
        try:
            await s.execute("SELECT 1")
        except Exception as e:
            print(f"bare 'SELECT 1'  -> {type(e).__name__}: {e}")
        break

asyncio.run(main())
```

```
text('SELECT 1') -> 1
bare 'SELECT 1'  -> ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')
```

**Expected:** `GET /health` returns 200 with `"postgres": "healthy"`, since the database answers `SELECT 1` on that same session.

**Actual:** the probe raises before it can report, the `except` block in `api/routes/health.py` sets `"postgres": "unhealthy"`, and the endpoint returns 503.

**One thing that is not this issue:** `"redis": "unhealthy"` in the same response comes from `'Settings' object has no attribute 'redis_host'`, not from SQLAlchemy, and it is already tracked as #62. I mention it only so the 503 above is not read as evidence about Redis, and so it is clear that wrapping the probe in `text()` will not by itself turn this endpoint green.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Before spending any credit I hand-graded `calib-02` against my draft rubric. Six of my seven
required checks failed it (`env-recorded`, `steps-rerunnable`, `artifact-present`,
`behavior-matches-issue`, `conclusion-honest`, `claim-specific-and-modest`), so it came out
`reject`, which matches the worksheet answer. That warm-up spent nothing and told me the
checks were executable before I paid for a run.

Two harness invocations before that graded nothing and produced no agreement score, because
the harness assumes a POSIX environment: the first died with `FileNotFoundError: [WinError 2]`
because Python's `CreateProcess` cannot launch the npm `claude.cmd` shim, and the second died
with `UnicodeEncodeError: 'charmap' codec can't encode character '\U0001f6a7'` when it piped
`pkg-11` (which contains an emoji) through a cp1252 stdin. I fixed both from the outside
rather than editing the harness: I put the real `claude.exe` on `PATH` (it ships inside
`node_modules/@anthropic-ai/claude-code/bin/`) and set `PYTHONUTF8=1`. I list these because
they are runs I started, but neither returned a verdict for any package.

Scored runs, in order:

1. **6/6** — partial run, `--only pkg-03,pkg-09,pkg-11,pkg-16,pkg-19,pkg-20`. I picked these
   six deliberately rather than running the first N: one package per hazard I thought my
   rubric was most likely to get wrong. `pkg-20` for the disclosure category (the one-package
   category the floor exists for), `pkg-09` for an honest cannot-reproduce that must be
   accepted and whose policy scopes disclosure to pull requests only, `pkg-11` for a terse
   accept my checks might over-reject on thinness, `pkg-16` for an unstated version deviation,
   `pkg-19` for a good report behind a boilerplate claim, and `pkg-03` for a policy that
   demands human-voiced comments. All six agreed, and reading the per-check output confirmed
   they agreed for the right reasons and not by luck: `pkg-20` failed on `repo-conventions`
   alone, `pkg-16` on the three version-deviation checks, `pkg-19` on
   `claim-specific-and-modest`.
2. **20/20** — the confirming full run, with `--save-run eval-run.txt`. Every category matched
   (`clear-accept 8/8`, `disclosure 1/1`, `no-evidence 4/4`, `unfollowable-comms 3/3`,
   `wrong-target 4/4`).

The committed `eval-run.txt` reports:

`agreement: 20/20 scored items  (bar: 18/20: PASS)`

**Package analysis**

`pkg-20` (ghostty-org/ghostty#13604). My rubric returned **reject**; the gold label is
**reject**. This is the package the category floor exists for, and it is worth explaining
because it is the only one in the set where every proof check passes and the package still is
not ready to post.

Seven of my eight checks passed it, and they were not close calls. `env-recorded` passed on
"ghostty 1.3.1 (release build, Fedora 42 RPM), GTK backend, GNOME 48 (Wayland), dark system
scheme" — version plus every fact the issue's GTK/dark-mode behavior turns on.
`artifact-present` and `behavior-matches-issue` passed on the two captured transcripts,
`^[[?997;2n` from the single-theme run and `^[[?997;1n` from the conditional-pair run, which
are the issue's exact symptom produced by the issue's exact trigger. `control-or-contrast`
passed too, since the conditional-pair run is a real control.

The single failure was `repo-conventions`. The repo-facts block states that ghostty's
`AI_POLICY.md` requires that "All AI usage in any form must be disclosed, stating the tool
used and the extent of the assistance", and adds that "AI-assisted issues and comments must be
reviewed and edited by a human before submission". That duty is not scoped to pull requests or
to code; it covers comments explicitly. Neither the claim comment nor the repro report says
anything about AI. My rubric treats these packages as AI-assisted work, so silence is
non-disclosure rather than a true statement that no AI was used, and `repo-conventions` is
`required`, so one fail rejects the package.

My rubric read it that way because I wrote the check to read the policy's *scope* instead of
asking a yes/no "is there an AI policy". That distinction is what makes the check survive the
rest of the set. Three other packages have AI policies that a blunter check would have fired
on wrongly: `pkg-05` (conda) is permissive — generative AI is welcome, you are responsible for
what you submit — and `pkg-09` (fd) requires the tool and extent to be stated *in the pull
request* and says outright there is no disclosure ask for issue comments. Both are gold
accepts, and both would have flipped to reject under a check that demanded disclosure whenever
the words "AI policy" appear. Meanwhile `pkg-03` (ripgrep) demands comments be human-voiced
rather than disclosed, which is a different condition again and passed on its own terms. So
the check has to fire on exactly one of the twenty, and the thing that makes it fire there and
nowhere else is reading how far each policy reaches.

**Check rationale**

The `repo-conventions` row from `tools/repro-check/rubric.md`, exactly as it reads now:

> | repo-conventions | The repo-facts block's stated contribution and AI policy, read as a condition on the two comments being posted. | Pass if the comments satisfy every condition that policy places on comments. Where the policy requires that AI assistance be disclosed, the comments must disclose it, naming the tool and the extent of its use; the packages graded here are AI-assisted work, so saying nothing is non-disclosure and fails. Where the policy requires that comments to maintainers be in the contributor's own words, the comments must read as a person's own account rather than generated filler. Where the stated policy places no condition on comments — no AI policy at all, a permissive "you are responsible for what you submit" policy, or a policy whose disclosure requirement is scoped to pull requests or code rather than to issue comments — this check passes on that ground, and no disclosure is required for it to pass. | required |

It reads that way because of what I rejected on the way to it. My first draft of this check
was, in substance, "if the repo has an AI policy, the comments must disclose AI use." Grading
the repo-facts lines by hand showed that this would be wrong more often than right. Fourteen
of the twenty packages state no AI policy at all, where demanding disclosure invents a rule
the project does not have; and of the six that do state one, only `pkg-20` actually requires
disclosure in comments. The other five state a responsibility without a disclosure duty
(`pkg-05`, `pkg-12`), a disclosure duty scoped to pull requests with issue comments explicitly
excluded (`pkg-09`), a limit on fully AI-generated contributions rather than a disclosure ask
(`pkg-07`), or a voice requirement rather than a disclosure one (`pkg-03`). Every one of those
five is a gold accept, so a check keyed to the mere presence of a policy would have taken five
accepts down with it.

So I rewrote the pass condition around the policy's reach rather than its existence, and spelt
the three passing grounds out by name — no policy, a permissive policy, a policy scoped to
pull requests or code — so the grader does not have to infer them. The clause that the
packages are AI-assisted work is there for the opposite failure: without it, a grader can read
a comment that never mentions AI as simply having nothing to disclose, and then `pkg-20`
passes and the disclosure category goes to 0/1. I also made the clause explicit in
`references/evidence-guide.md`, which walks the same five policy shapes and ends with the rule
that decides them: quote the policy line before grading against it, and let its own words set
how far it reaches.

**Trade-offs**

The trade-off I can measure is `control-or-contrast`, the one check I marked `preferred`
instead of `required`. A contrasting run — the same command with the trigger removed — is the
single strongest thing a report can carry, and my first instinct was to require it.

The full run shows what that would have cost. `control-or-contrast` came back `fail` or
`unclear` on 13 of the 20 packages, and three of those thirteen are gold **accepts**: `pkg-03`
("Contrast run (no -r flag) is narrated but its transcript isn't pasted"), `pkg-05` ("No
baseline/contrast run... is shown or explained"), and `pkg-12` ("No contrasting run... is
shown, and the report does not say why one is unavailable"). Had I weighted the check
`required`, all three would have flipped from accept to reject and my agreement would have
been 17/20 — below the bar — while not fixing a single package I was already getting wrong,
since every package it failed that gold also rejects was already being rejected by a
`required` check. Keeping it `preferred` costs me nothing on this set and still surfaces the
weakness in the readable summary, which is what I wanted the check for.

What I accept it will miss: a package that reproduces a symptom correctly but attributes it to
the wrong trigger, where a control run is the only thing that would have exposed the
mistake. If such a package also records its environment, shows its artifact, and states its
conclusion honestly, my rubric accepts it. `pkg-02` and `calib-03` are near-misses of exactly
this shape and my rubric catches both, but it catches them on `behavior-matches-issue` — the
artifact shows a graceful error where the issue reports a panic — not on the missing control.
A package whose wrong trigger happened to produce the issue's own error text would get through.

On the disclosure side I did not need a canary in the end, because I never loosened
`repo-conventions` after it was written: the conditional wording was in place before the first
scored run, `pkg-20` agreed in the 6-package partial run, and it agreed again in the full run.
That is the sense in which nothing changed elsewhere — the partial run deliberately carried
`pkg-20` (the whole `disclosure` category) plus `pkg-09` and `pkg-05`'s sibling cases precisely
so that a mis-scoped policy check would have shown up at about $1.20 rather than on the $4
confirming run, and it did not show up.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
