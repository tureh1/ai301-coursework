# Plan: #61 — health check DB probe passes a raw SQL string

Issue: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61
Builds on my repro: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5896400922
Base: `main` at `f89c06f`. Branch: `fix/61-health-probe-text`.

## Diagnosis

`api/routes/health.py:32` runs `await db.execute("SELECT 1")` on the
session from `get_db()`. SQLAlchemy 2.x does not coerce a bare string
into textual SQL, so the call raises `ArgumentError` before any query
reaches Postgres. The `except Exception` block around it then sets
`"postgres": "unhealthy"`, so a reachable database gets reported as down.

What my repro showed (Windows 11, Docker Desktop, `postgres:16-alpine`,
Python 3.12.10, SQLAlchemy 2.1.1, asyncpg 0.31.0):

- `GET /health` returned `HTTP 503` with `"postgres": "unhealthy"`, and
  the server log said why:

  ```
  [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"
  ```

- Control, same `get_db()` session in one run, only `text()` differs:

  ```
  text('SELECT 1') -> 1
  bare 'SELECT 1'  -> ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')
  ```

The control rules out the database and the connection. The same session
answers `SELECT 1` once the string is wrapped, so the bare string is the
only difference.

## Scope

In scope: the postgres probe on line 32 of `api/routes/health.py`, plus
one new unit test file that pins it.

Not in scope:

- The Redis probe. `"redis": "unhealthy"` in the same response comes from
  `'Settings' object has no attribute 'redis_host'`, which is #62. Because
  of that, `/health` will **still return 503 after this fix** until #62
  lands. This fix changes the `postgres` key, not the status code.
- The `pyproject.toml` mypy override for `api.routes.health`
  (`["attr-defined", "call-overload", "index"]`). I removed each code in
  turn and ran `mypy api/routes/health.py`. None of them is raised by
  line 32. `attr-defined` is `settings.redis_host`/`redis_port` (#62),
  `call-overload` is the `redis.Redis(...)` call on line 46 (#62), and
  `index` is the `health_status` dict typing. Removing any of them would
  turn CI red for code this fix does not touch.
- Narrowing `except Exception`, the `datetime.utcnow()` deprecation, and
  the vector-DB placeholder. They are separate concerns and this issue
  doesn't need them.

## Files

- `api/routes/health.py`: add `from sqlalchemy import text` and change
  line 32 to `await db.execute(text("SELECT 1"))`.
- `tests/unit/test_health.py` (new): unit tests that call the real
  `health_check` handler.

## Approach

1. Branch `fix/61-health-probe-text` from `main` at `f89c06f`.
2. Write `tests/unit/test_health.py` first and run it on unchanged code so
   it fails for the right reason. The test passes the handler a session
   backed by a real SQLAlchemy `Session` on in-memory SQLite (stdlib
   `sqlite3`, no new dependency), behind a two-line async `execute`
   wrapper. A `Mock`/`AsyncMock` session would accept the bare string and
   pass with the bug still there, so it can't pin anything. `Session.execute`
   rejects a bare string with the same `ArgumentError` the repro shows. The
   handler raises 503 because of #62, so the test reads
   `detail["dependencies"]["postgres"]` from either the returned dict or the
   `HTTPException`. That keeps the test correct before and after #62.
   - `test_postgres_probe_reports_healthy_for_reachable_db`: asserts
     `"healthy"`. Fails before the fix, passes after.
   - `test_postgres_probe_reports_unhealthy_when_db_errors`: a session
     whose `execute` raises `OperationalError` still gives `"unhealthy"`,
     so the fix can't hide a real outage.
3. Make the one-line change in `api/routes/health.py`.
4. Run `ruff`, `black --check`, `mypy`, and `pytest tests/unit -m unit`
   (what `make check && make test-unit` runs; `make` isn't installed on my
   machine, so I call the tools directly).
5. Commit as `fix(api): wrap health check DB probe in text()` with
   `Fixes #61`.

## Test plan

1. **My Unit 2 repro, re-run against the branch.** Same steps:
   `docker compose up -d`, `alembic upgrade head`, `uvicorn api.main:app`,
   then
   `curl -s -w "\nHTTP %{http_code}\n" http://127.0.0.1:8000/health`.
   - Before (posted in Unit 2): `HTTP 503`, `"postgres": "unhealthy"`,
     and a `postgres_health_check_failed` log line with the `ArgumentError`.
   - Expected after: still `HTTP 503` (Redis, #62), but
     `"postgres": "healthy"`, and no `postgres_health_check_failed` line in
     the server log. The only remaining error line is
     `redis_health_check_failed`.
2. **The control, run through the real code.** My Unit 2 control script
   calls `session.execute` directly, so it never touches `health.py` and
   gives the same output with or without the fix. Its inputs (a live
   session, the bare string, then `text()`) become the new unit test. That
   test calls the real handler:
   - Before the fix:
     `test_postgres_probe_reports_healthy_for_reachable_db` fails with
     `'unhealthy' == 'healthy'`.
   - After: both tests pass.
3. The unit suite and lint/format/type checks stay green. No new mypy
   finding, and no new failures beyond the existing xfail markers.

## Risks and unknowns

- **The endpoint still returns 503.** A reviewer who checks only the
  status code will see no change until #62 is fixed. That's why the test
  plan watches the `postgres` key and the log line, not the code.
- **The unit test runs on SQLite, not Postgres.** The `ArgumentError` is
  raised in SQLAlchemy's statement coercion before any dialect is
  involved, so the backend shouldn't matter. That is reasoning, though,
  not proof. Test plan step 1 covers real Postgres through asyncpg.
- **The test wraps a sync `Session` rather than using `AsyncSession`.**
  This avoids adding `aiosqlite` to the dev dependencies. My unverified
  assumption is that `AsyncSession.execute` goes through the same coercion
  step. Step 1 checks that on the real async path.
- **Overlap with classmates.** Other students have posted plans for the
  same line. Under the house rules that doesn't block this plan, but any
  PRs that land will conflict on line 32.
- **Overlap with open PR #93 (for #62).** It also creates
  `tests/unit/test_health.py` and edits the same mypy override. Its body
  says `call-overload` and `index` "belong to #61", which my mypy run
  contradicts. If #93 merges first, I'll rebase and add my tests to its
  file instead of creating a second one.

## Deviations

The code went in exactly as planned. Commit `cde7071` on
`fix/61-health-probe-text` changes two lines of `api/routes/health.py`
(the `text` import and line 32) and adds `tests/unit/test_health.py`
with the two tests named above. Nothing else changed, and the mypy
override was left alone. Two smaller things differ from what the plan
said:

- **How I ran mypy.** On my machine, `mypy` with the repo's
  `python_version = "3.11"` stops before checking anything. It errors out
  in numpy's installed stub (`numpy/__init__.pyi:737: Type statement is
  only supported in Python 3.12 and greater`). That happens on `main`
  too, so it's my environment, not this change. I ran the Makefile's
  target list with `--python-version 3.12` instead, on `main` and on the
  branch. Both report `Success: no issues found in 76 source files`, so
  there's no new finding. I didn't touch the config. CI's own Python
  decides the real result.
- **One unknown resolved.** The plan flagged as unverified whether the
  real `AsyncSession` path goes through the same coercion as the sync
  `Session` in the unit test. The live re-run settled it. Through
  asyncpg on Postgres, the branch logs `postgres_health_check_passed`
  where `main` logs the `ArgumentError`.

The plan comment (`comment.md`) still describes what I built, so posting
it as written needs no follow-up correction.
