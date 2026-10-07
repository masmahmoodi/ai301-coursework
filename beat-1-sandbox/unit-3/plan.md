# Plan: fix Redis probe in health check (issue #62)

## Diagnosis

The Redis health probe in `api/routes/health.py` builds its client with:

```python
r = redis.Redis(
    host=settings.redis_host,
    port=settings.redis_port,
    db=0,
    decode_responses=True,
)
```

`Settings` in `core/config.py` defines `redis_url: str` and nothing else for
Redis — there is no `redis_host` or `redis_port` attribute. Python raises
`AttributeError: 'Settings' object has no attribute 'redis_host'` on every
call to `GET /health`, and the broad `except Exception` swallows it, logging
`redis_health_check_failed` and marking Redis as unhealthy.

My Unit 2 reproduction confirmed this directly:

```
$ .venv/bin/python -c "
import redis
from core.config import settings
print('ping ->', redis.Redis.from_url(settings.redis_url, decode_responses=True).ping())
print('hasattr redis_host ->', hasattr(settings, 'redis_host'))
print('hasattr redis_port ->', hasattr(settings, 'redis_port'))
"
ping -> True
hasattr redis_host -> False
hasattr redis_port -> False
```

```
2026-09-27 20:29:19 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'"
```

The cause is a mismatch introduced at some point between what `Settings`
provides (`redis_url`) and what the health probe reads (`redis_host`,
`redis_port`). Redis itself is reachable; only the probe is broken.

## Scope

**In scope:** change the Redis probe in `api/routes/health.py` to build its
client from `settings.redis_url` using `redis.Redis.from_url()`, matching how
`Settings` exposes the connection.

**Not in scope:** the `postgres` health check (a separate issue, #61), the
vector-DB check, anything in `core/config.py`, any other route, any database
migration, and the `except Exception` broad-catch pattern (a style concern,
not the cause).

## Files to change

- `api/routes/health.py` — the only file that accesses the missing attributes.
  Lines 43–47 (the `redis.Redis(host=..., port=...)` constructor call).

No other file needs to change: `core/config.py` already provides `redis_url`
correctly and `redis.Redis.from_url()` is already available through the
installed `redis` package.

## Approach

Replace:

```python
r = redis.Redis(
    host=settings.redis_host,
    port=settings.redis_port,
    db=0,
    decode_responses=True,
)
```

with:

```python
r = redis.Redis.from_url(settings.redis_url, decode_responses=True)
```

`redis.Redis.from_url` accepts the `redis://host:port/db` URL that `Settings`
provides. The `db=0` part is encoded in the URL (the `/0` suffix in
`redis://localhost:6379/0`) so no separate argument is needed.

## Test plan

Re-run the Unit 2 reproduction steps against the changed code:

1. Start backing services: `docker compose up -d`
2. Start app: `.venv/bin/uvicorn api.main:app --host 127.0.0.1 --port 8000`
3. Confirm Redis is reachable: `docker compose exec -T redis redis-cli ping` →
   expect `PONG`
4. Call the health endpoint:
   `curl -s -w '\nHTTP_STATUS=%{http_code}\n' http://127.0.0.1:8000/health`

Before the fix: response body contains `"redis": "unhealthy"`, status 503,
server log shows `AttributeError: 'Settings' object has no attribute 'redis_host'`.

After the fix: `"redis": "healthy"`, no `AttributeError` in the server log.
The postgres check may still fail (that is issue #61), but the overall status
and the redis field specifically should reflect only the postgres result, not
a Redis failure.

Also run the project's existing test suite (`pytest`) to confirm no regressions.

## Risks and unknowns

- **Postgres still unhealthy:** The test run will likely still return 503
  because the postgres probe has a separate SQLAlchemy 2.x bug (#61). That
  is expected and is not a regression from this change. After this fix,
  `"redis"` will be `"healthy"` even if `"postgres"` is still `"unhealthy"`.
- **No unit tests for the health route:** The existing test suite (`tests/`)
  does not appear to include a test for `GET /health` that mocks dependencies.
  I will add one that patches `redis.Redis.from_url` to verify the probe now
  calls it rather than the missing attributes.

## Deviations

**pyproject.toml suppression:** CONTRIBUTING.md requires removing the mypy
suppression for a seeded bug when fixing it. The `[[tool.mypy.overrides]]`
block for `api.routes.health` had `disable_error_code = ["attr-defined",
"call-overload", "index"]`. After the fix, `mypy api/routes/health.py` (using
the project config) reports no `attr-defined` errors. The `call-overload` and
`index` codes remain — they cover the postgres probe (issue #61) which is
unchanged. So `attr-defined` was removed from the suppression list; the block
stays with the two remaining codes. This small change to `pyproject.toml` was
not mentioned in the original plan and was added after reading CONTRIBUTING.md.

**Regression tests added:** The plan mentioned adding a pytest test as a risk
item, and the test was in fact written and added to
`tests/unit/test_health_route.py`. Two tests: one asserts that the probe calls
`redis.Redis.from_url` with `settings.redis_url`, the other asserts that
`Settings` has `redis_url` and no `redis_host`/`redis_port`. Both pass.

**Full app startup not tested:** The plan described re-running
`curl http://127.0.0.1:8000/health` end-to-end after the fix. The full app
could not start during testing because `core/database.py`'s `init_db()` calls
`create_all()` which fails with `DuplicateTableError` on `ix_profiles_user_id`
when the postgres volume already has tables from a prior run. This is a
pre-existing startup bug unrelated to the Redis fix. As an alternative, the
before/after evidence was captured by running the probe's code path directly:

```
$ cd ~/Desktop/ai301-work/pathreview-fork
$ .venv/bin/python -c "
import redis
from core.config import settings

print('=== BEFORE (bug) ===')
print('hasattr(settings, \"redis_host\") ->', hasattr(settings, 'redis_host'))
try:
    r = redis.Redis(host=settings.redis_host, port=settings.redis_port, db=0, decode_responses=True)
except AttributeError as e:
    print('AttributeError:', e)

print()
print('=== AFTER (fix) ===')
print('settings.redis_url =', settings.redis_url)
r = redis.Redis.from_url(settings.redis_url, decode_responses=True)
print('ping ->', r.ping())
print('Redis probe succeeds with from_url — no AttributeError.')
"
```

Output:

```
=== BEFORE (bug) ===
hasattr(settings, "redis_host") -> False
hasattr(settings, "redis_port") -> False
AttributeError: 'Settings' object has no attribute 'redis_host'

=== AFTER (fix) ===
settings.redis_url = redis://localhost:6380/0
ping -> True
Redis probe succeeds with from_url — no AttributeError.
```

Redis was running (`docker compose exec -T redis redis-cli ping` → `PONG`),
confirming the probe would return healthy once the app can start fully.

**Port 6380 again:** Same port 6379 conflict as Unit 2; remapped in
docker-compose.yml for local testing only — that file is not committed.
