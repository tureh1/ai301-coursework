# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

tureh1

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-6019047568

Plan, building on [my reproduction above](https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5896400922) (`main` at `f89c06f`, Windows 11 with Docker Desktop, Python 3.12.10, SQLAlchemy 2.1.1, asyncpg 0.31.0, `postgres:16-alpine`).

**Cause.** `api/routes/health.py:32` passes the bare string to `db.execute("SELECT 1")`. In my control run the same `get_db()` session gave:

```
text('SELECT 1') -> 1
bare 'SELECT 1'  -> ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')
```

So the database is reachable, the bare string raises, and the `except` block turns that into `"postgres": "unhealthy"`.

**Change.** Import `text` from `sqlalchemy` and make line 32 `await db.execute(text("SELECT 1"))`. Add `tests/unit/test_health.py`, which calls the real `health_check` handler with a real SQLAlchemy `Session` on in-memory SQLite. A mock session would accept the bare string, so it couldn't catch this. One test asserts `postgres` is `healthy` for a working DB (it fails on `main` today). The other asserts it stays `unhealthy` when `execute` raises `OperationalError`.

**Not changing:**
- The Redis probe. That's #62, so `/health` will still return 503 after this fix. The change shows up in the `postgres` key and the log, not in the status code.
- The mypy override for `api.routes.health`. I removed each code in turn and ran mypy. None of `attr-defined`, `call-overload`, or `index` comes from line 32: the first two are the Redis lines (#62), and `index` is the dict typing. Removing any of them would break `typecheck` on code this fix doesn't touch.

**Overlap with #93.** The open PR for #62 also creates `tests/unit/test_health.py`, and it says `call-overload` and `index` belong to #61. By my mypy run they don't come from line 32 (`call-overload` is the `redis.Redis(...)` call #93 replaces). So I'm leaving both codes alone either way. If #93 merges first, I'll rebase and add my two tests to its file rather than create a second one. I've also seen the other plans for line 32 on this thread. Some of them drop `call-overload` from the override, and the mypy runs above are why I'm not.

**Test plan.** Re-run my repro (`uvicorn` + `curl /health`). I expect `"postgres": "healthy"` and no `postgres_health_check_failed` line in the log, with 503 remaining because of Redis. Then run the new tests before and after the change, plus ruff/black/mypy and `pytest tests/unit -m unit`.

**Unknowns.** The unit test runs on SQLite through a small async wrapper around a sync `Session`, not on asyncpg. My understanding is that the `ArgumentError` comes from SQLAlchemy's coercion step before any dialect is involved, but the live `curl` re-run is what checks the real Postgres path.

I used Claude Code to help draft this plan and build the change. Every command ran on my machine, and I reviewed the outputs and the diff.

---

## Your branch

**Branch**

fix/61-health-probe-text

**Evidence**

**1. My Unit 2 repro, re-run against the change.** Same stack as Unit 2 (Windows 11, Docker Desktop 29.1.3, `postgres:16-alpine`, Python 3.12.10, SQLAlchemy 2.1.1, asyncpg 0.31.0), brought up with `docker compose up -d`, `.venv/Scripts/alembic upgrade head`, and `.venv/Scripts/python -m uvicorn api.main:app --host 127.0.0.1 --port 8000`. Both runs below are from the same session on 2026-10-06. "Before" is `main` at `f89c06f` with my change stashed, and "after" is the branch.

Before (`main`, `f89c06f`):

```
$ git rev-parse --short HEAD && git diff --stat
f89c06f

$ curl -s -w "\nHTTP %{http_code}\n" http://127.0.0.1:8000/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-10-06T14:49:02.573683"}}
HTTP 503
```

Server log:

```
2026-10-06 09:49:02 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')" request_id=0ea37172-3dcf-4b74-9e5e-dd43f51d6f6e
2026-10-06 09:49:02 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'" request_id=0ea37172-3dcf-4b74-9e5e-dd43f51d6f6e
2026-10-06 09:49:02 [debug    ] vector_db_health_check_passed  request_id=0ea37172-3dcf-4b74-9e5e-dd43f51d6f6e
INFO:     127.0.0.1:59062 - "GET /health HTTP/1.1" 503 Service Unavailable
```

After (`fix/61-health-probe-text`, the fix applied):

```
$ git rev-parse --short HEAD && git diff --stat
f89c06f
 api/routes/health.py | 3 ++-
 1 file changed, 2 insertions(+), 1 deletion(-)

$ curl -s -w "\nHTTP %{http_code}\n" http://127.0.0.1:8000/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"healthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-10-06T14:49:05.902787"}}
HTTP 503
```

Server log:

```
2026-10-06 09:49:05 [debug    ] postgres_health_check_passed   request_id=45332320-cda5-4b13-9e9d-9ca0ea171552
2026-10-06 09:49:06 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'" request_id=45332320-cda5-4b13-9e9d-9ca0ea171552
2026-10-06 09:49:06 [debug    ] vector_db_health_check_passed  request_id=45332320-cda5-4b13-9e9d-9ca0ea171552
INFO:     127.0.0.1:59067 - "GET /health HTTP/1.1" 503 Service Unavailable
```

`"postgres"` changes from `"unhealthy"` to `"healthy"`, and `postgres_health_check_failed` is replaced by `postgres_health_check_passed`. The 503 stays, as the plan predicted, because the Redis probe still fails on `redis_host`, which is #62.

**2. My Unit 2 control, turned into a check through the real code.** The Unit 2 control script calls `session.execute` directly and never imports `health.py`, so its output is the same with or without the fix. I turned its inputs (a live SQLAlchemy session, the bare string vs. `text()`) into `tests/unit/test_health.py`, which calls the real `health_check` handler.

Before (test file added, fix not yet applied):

```
$ .venv/Scripts/python -m pytest tests/unit/test_health.py -v -m unit -p no:warnings
collecting ... collected 2 items

tests/unit/test_health.py::TestHealthPostgresProbe::test_postgres_probe_reports_healthy_for_reachable_db FAILED [ 50%]
tests/unit/test_health.py::TestHealthPostgresProbe::test_postgres_probe_reports_unhealthy_when_db_errors PASSED [100%]

================================== FAILURES ===================================
_ TestHealthPostgresProbe.test_postgres_probe_reports_healthy_for_reachable_db _
>       assert await _postgres_status(sqlite_session) == "healthy"
E       AssertionError: assert 'unhealthy' == 'healthy'

tests\unit\test_health.py:64: AssertionError
---------------------------- Captured stdout call -----------------------------
2026-10-06 09:46:01 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"
2026-10-06 09:46:01 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'"
2026-10-06 09:46:01 [debug    ] vector_db_health_check_passed
=========================== short test summary info ===========================
FAILED tests/unit/test_health.py::TestHealthPostgresProbe::test_postgres_probe_reports_healthy_for_reachable_db
========================= 1 failed, 1 passed in 1.08s =========================
```

After (fix applied):

```
$ .venv/Scripts/python -m pytest tests/unit/test_health.py -v -m unit -p no:warnings
collecting ... collected 2 items

tests/unit/test_health.py::TestHealthPostgresProbe::test_postgres_probe_reports_healthy_for_reachable_db PASSED [ 50%]
tests/unit/test_health.py::TestHealthPostgresProbe::test_postgres_probe_reports_unhealthy_when_db_errors PASSED [100%]

============================== 2 passed in 0.91s ==============================
```

**3. The repo's checks on the branch:**

```
$ .venv/Scripts/python -m ruff check .
All checks passed!
$ .venv/Scripts/python -m black --check .
111 files would be left unchanged.
$ .venv/Scripts/python -m pytest tests/unit -m unit -q -p no:warnings
377 passed, 53 xfailed in 9.47s
$ .venv/Scripts/python -m mypy --python-version 3.12 api/ core/ ingestion/ rag/ agent/ safety/
Success: no issues found in 76 source files
```

(`main` gives the same mypy result. The `--python-version 3.12` flag is explained in the Deviations section of `plan.md`.)

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Three scored runs, in order. Each run graded with my installed `~/.claude/skills/plan-check/`
files. Before every run I set `PYTHONUTF8=1` and put the real `claude.exe` on `PATH`, the same
Windows workarounds as Unit 2, so no run failed before grading.

1. **5/6**, a partial run with `--only pkg-04,pkg-09,pkg-14,pkg-15,pkg-20,pkg-03`. I chose these
   six because they were the ones most likely to break my first draft. `pkg-04` and `pkg-20` are
   the whole `thread-convention` category. `pkg-09` and `pkg-14` are the two "honestly
   scoped-down" accepts that a strict scope or executability check could reject. `pkg-15` is the
   scope-creep package the gold note calls arguable "because the core fix is right". `pkg-03`
   has an AI policy that asks for human-voiced comments rather than disclosure. Five agreed. The
   one miss was `pkg-14`, a gold accept, which failed `diagnosis-grounded`.
2. **3/3**, a partial run with `--only pkg-14,pkg-16,pkg-01,calib-03 --include-calibration`,
   after I loosened `diagnosis-grounded`. `pkg-14` flipped to accept. `pkg-01` and `pkg-16` were
   canaries from `wrong-cause`, the category a looser diagnosis check could leak, and both stayed
   reject. `calib-03`, the worksheet's wrong-cause trap, also stayed reject. It is unscored, so
   the score is 3/3.
3. **20/20**, the confirming full run with `--save-run eval-run.txt`. Every category matched:
   `clear-accept 7/7  scope-creep 4/4  thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`.

The committed `eval-run.txt` reports:

`agreement: 20/20 scored items  (bar: 18/20: PASS)`

**Package analysis**

`pkg-14` (zellij-org/zellij#5174). My rubric's final verdict was **accept**, and the gold label is
**accept**. I picked it because it is the one package my rubric got wrong first, and the reason
it got it wrong is what changed my diagnosis check.

The plan blames the reattach handshake: "the reattach path wires the client's stdin to the
session before the query responses have been consumed, so the responses arrive as ordinary input
and are echoed into the active pane". The repro supports that on its main points. A fresh attach
is clean, every reattach leaks, and the "Same host and terminal on zellij 0.44.1: 5 reattach
cycles, no leak" control places the change in 0.44.2. One control is harder to fit: "After
`rm -rf ~/.cache/zellij` ... the next attach is clean, the one after leaks again." The plan
explains it with an assumption it never tests: "with an empty cache the color data is refetched
along the fresh-attach path once."

On the first partial run my rubric failed `diagnosis-grounded` with this evidence: "Cache control
... is explained only by the plan's unverified claim ... an invented bridge with no evidence."
That grade was wrong, and the gold note shows why: the plan is "grounded in the repro and the
regression-window". No step in the evidence *rules out* the handshake cause. The cache control
is one the plan explains weakly, but it never shows the handshake working while the leak
happens. My first wording asked the cause to be "consistent with every step and control". The
grader read that as "positively explains every step" and penalized a hypothesis. That is the
honesty problem `unknowns-stated` exists to report, and it is not a wrong cause.

After the revision, the full run passed all seven checks on `pkg-14`. `diagnosis-grounded`
passed because no control contradicts the stated cause. `executable` passed because the plan
names "the client attach/reattach path in `zellij-server`" and one mechanism ("consume or drain
pending OSC query responses ... before pane input is wired"), and leaves only the exact function
to pin, which my rubric allows as "one narrowly named unknown". `scope-bounded` passed because
the Windows variant is "Explicitly deferred, with reasons". Deferral is the passing shape in my
rubric, not a gap. `test-decisive` passed on "5 consecutive SSH reattach cycles with no rgb
strings in any pane".

**Check rationale**

The `diagnosis-grounded` row from `tools/plan-check/rubric.md`, exactly as it reads now:

> | diagnosis-grounded | The plan's stated cause (and the cause the plan comment asserts), read step by step against the repro evidence block: every control run, every contrast, and every step that says where the behavior appears or disappears. | Pass if no step or control in the repro evidence contradicts the stated cause, so that the change the plan makes is at the place the evidence points to. A cause need not explain every observation in full: a step the plan explains with a hypothesis it has not tested, or does not explain at all, is not a contradiction and does not fail this check. Fail only if a repro step or control rules the stated cause out: a control where the blamed component works correctly under the same conditions, a step showing the damage has already happened before the blamed code runs, or a step showing the behavior with the blamed component absent. Also fail if the plan dismisses a variable the evidence shows deciding the outcome (calling it a "red herring" or "side effect") without evidence of its own. Agreeing with the issue, the thread, or a confident commenter does not pass this check on its own: where the repro evidence and a stated diagnosis disagree, the repro evidence wins. | required |

It reads that way because the wrong-cause packages in this set all fail in the same way. Each
plan blames a component that one of its own repro steps shows to be working. `pkg-01` blames
the request-item tokenizer, but its control parses the same items fine without `-v`. `pkg-07`
says the friendly-error system is tree-shaken out, but its control prints a friendly error from
an instance method in the same build. `pkg-11` blames the collect operator, but its top-level
`[.a]` returns length 1. `pkg-16` blames the post-read cast, but step 4 shows the zeros "already
gone in the parsed table, before any cast to string could run". So the three named fail shapes
(blamed component works under the same conditions, damage done before the blamed code runs,
behavior with the blamed component absent) come from those four packages. In the full run, all
four failed on exactly one of those shapes.

Two clauses are there for traps. "Agreeing with the issue, the thread, or a confident commenter
does not pass this check on its own" is for `calib-03`, where the plan adopts the thread's
key-binding diagnosis while the repro's timing matrix points at highlighting. The "red herring"
clause is for `pkg-01`, whose plan waves off the Python-version difference that the repro shows
deciding the outcome.

The sentence "A cause need not explain every observation in full ... is not a contradiction"
came from the `pkg-14` miss described above. My first version said "consistent with every step
and control". That reads naturally, but in practice the grader treated an untested explanation
as a failure. "Contradicts" is the narrower word I meant from the start, and "Fail only if"
makes the grader find a ruling-out step before failing. I also changed `procedure.md` to match.
Its question is now "if the plan's stated cause were true, would this step have come out the way
it did?" A "possibly, but untested" answer goes to `unknowns-stated` instead.

**Trade-offs**

Loosening `diagnosis-grounded` from "consistent with every step" to "contradicted by a step"
made a package flip possible, so I re-ran canaries with `--only` before spending $4 on the full
run. A looser diagnosis check can only leak in the `wrong-cause` direction, so I re-ran `pkg-01`
and `pkg-16` from that category, plus `calib-03` as a free trap check. `pkg-16` mattered most
because it is the subtlest: "long and confident", with the contradicting fact buried in step 4
of a four-step matrix. All three stayed reject, and the full run then held `wrong-cause` at 4/4.
The loosening cost nothing on this set.

What I accept the check will now miss is a plan whose cause has no support in the repro and
also isn't contradicted by any step. Take a repro with no controls: a single failing run, and a
plan that blames some plausible function. My check passes that, because nothing in the evidence
rules the cause out. The rubric treats "unsupported" as an honesty problem (`unknowns-stated`,
which is `preferred`), not a grounding failure, so it is reported and never holds the plan. Every
repro in this eval set includes at least one control, so the set never tests this case. A real
thread with a bare one-run repro would.

The live run on my own plan found a second blind spot, this time in `thread-aware` and my
procedure. The procedure only looks for pull requests "named in the thread". When I graded my
#61 plan, the skill found open PR #93 (for #62) by searching the repo itself, and reported it as
a procedure gap: "`procedure.md` only looks at PRs 'named in the thread'. So it missed PR #93."
#93 creates the same test file I planned and edits the same mypy override. I added a paragraph
about it to my comment. I did not change the procedure, because that would have changed the
graded files after the confirming run and made `eval-run.txt` no longer describe the skill I'm
uploading. In eval mode the bundle is the whole world, so the gap only shows up in live mode.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
