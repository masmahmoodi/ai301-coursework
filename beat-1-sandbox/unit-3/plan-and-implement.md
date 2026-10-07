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

masmahmoodi

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62#issuecomment-6047078277

Posted by masmahmoodi at 2026-10-07T21:20:06Z. Exact text as posted:

```
I reproduced this on `main` at `f89c06f` (full report above). The Redis probe in
`api/routes/health.py` builds its client with `settings.redis_host` /
`settings.redis_port`, but `Settings` in `core/config.py` only defines
`redis_url`. The `AttributeError` that raises is swallowed by the probe's
`except Exception`, so `GET /health` always marks Redis as unhealthy even
with Redis running and reachable.

My plan is to replace the `redis.Redis(host=..., port=...)` constructor with
`redis.Redis.from_url(settings.redis_url, decode_responses=True)` in
`api/routes/health.py`. That is the only file that reads the missing
attributes, and `redis.Redis.from_url` accepts the URL that `Settings`
already provides.

To test it, I will re-run `curl http://127.0.0.1:8000/health` after the
change and confirm `"redis"` reports `"healthy"` and the `AttributeError`
is gone from the server log.
```

---

## Your branch

**Branch**

fix/62-redis-url

**Evidence**

BEFORE (from Unit 2 reproduction, commit `f89c06f`, unmodified codebase):

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
$ curl -s -w '\nHTTP_STATUS=%{http_code}\n' http://127.0.0.1:8000/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-09-28T03:29:19.210950"}}
HTTP_STATUS=503
```

Server log for that request:
```
2026-09-27 20:29:19 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'"
```

AFTER (branch `fix/62-redis-url`, commit `37c330c`, from the fork's directory):

```
$ .venv/bin/python -c "
import redis
from core.config import settings

print('=== BEFORE (bug path) ===')
print('hasattr(settings, \"redis_host\") ->', hasattr(settings, 'redis_host'))
try:
    r = redis.Redis(host=settings.redis_host, port=settings.redis_port, db=0, decode_responses=True)
except AttributeError as e:
    print('AttributeError:', e)

print()
print('=== AFTER (fix path) ===')
print('settings.redis_url =', settings.redis_url)
r = redis.Redis.from_url(settings.redis_url, decode_responses=True)
print('ping ->', r.ping())
print('Redis probe succeeds with from_url — no AttributeError.')
"
```

Output:
```
=== BEFORE (bug path) ===
hasattr(settings, "redis_host") -> False
hasattr(settings, "redis_port") -> False
AttributeError: 'Settings' object has no attribute 'redis_host'

=== AFTER (fix path) ===
settings.redis_url = redis://localhost:6380/0
ping -> True
Redis probe succeeds with from_url — no AttributeError.
```

Redis reachability confirmed:
```
$ docker compose exec -T redis redis-cli ping
PONG
```

Regression test run (unit suite, branch `fix/62-redis-url`):
```
$ .venv/bin/pytest tests/unit/ -v --tb=no -q
...
377 passed, 53 xfailed, 8 warnings in 28.40s
```

The full app could not start due to a pre-existing `DuplicateTableError` on
`ix_profiles_user_id` in `core/database.py`'s `init_db()` — unrelated to the
Redis fix (see plan.md Deviations). End-to-end `curl` evidence was therefore
replaced with direct code-path evidence above. The two new unit tests in
`tests/unit/test_health_route.py` exercise the actual health route code with
mocked dependencies:

```
$ .venv/bin/pytest tests/unit/test_health_route.py -v
tests/unit/test_health_route.py::TestHealthRoute::test_redis_probe_uses_from_url PASSED
tests/unit/test_health_route.py::TestHealthRoute::test_redis_probe_no_redis_host_attribute PASSED
2 passed, 2 warnings in 4.24s
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Two runs, both full 20-package runs on the Sonnet grader the harness pins.

1. Run 1 (first try, `--out /tmp/u3run1.json`, no `--save-run`): `agreement: 20/20 scored
   items  (bar: 18/20: PASS)`, with `categories: clear-accept 7/7  scope-creep 4/4
   thread-convention 2/2  unbuildable 3/3  wrong-cause 4/4`.
2. Run 2 (final, `--save-run eval-run.txt`): `agreement: 20/20 scored items  (bar:
   18/20: PASS)`, same category line. That is the run saved in `eval-run.txt`.

No `--only` runs were needed. Run 1 produced no disagreements, so there was nothing to
re-check. The rubric, evidence guide, and procedure were not revised between the two
runs — run 2 is the same files run again to produce the saved transcript. The
`rubric.md sha256:3598dd4738778f21` and `evidence-guide.md sha256:e0ef06f3f73fa11f` in
the `eval-run.txt` header match the files now in `tools/plan-check/`.

**Package analysis**

`pkg-05` (`conda/conda#16502`). My rubric said **accept**; the gold label is **accept**.
They agree.

`pkg-05` is a textbook clear-accept and I chose it to show what all five required checks
look like when they pass without friction:

- **diagnosis-grounded:** The plan states "cache freshness in `conda/notices/cache.py` is
  derived only from the cached notices' own `expires_at` values, so one long-lived notice
  pins the whole channel response." The repro evidence confirms this exactly: step 4 shows
  no network request when notice B is added (access log: one request total), and the
  control run (cache deleted) shows both notices printing. The cause matches what the
  evidence demonstrates.

- **scope-bounded:** "Scope, one bounded change: add a maximum cache age to the freshness
  check in `get_notice_response_from_cache()`, using the existing
  `NOTICES_DECORATOR_DISPLAY_INTERVAL` constant (24h) as the cap, per the direction
  already settled in the thread. Not in scope: the notice display cadence itself, channel
  protocol changes, or cache storage format." One function, one constant, one test file.

- **executable:** Files named: `conda/notices/cache.py` and `tests/notices/test_cache.py`.
  Approach stated: compute age from stored timestamp, compare against constant, treat stale
  when exceeded. A stranger can open the named function and start.

- **test-decisive:** "re-run the repro scenario with the cache file's timestamp backdated
  by 25 hours: step 4 must fetch and print A and B (access log shows the second request).
  Un-backdated, step 4 must stay served from cache." Observable: whether the access log
  shows a second request. Directly re-runs the repro artifact.

- **thread-aware:** Thread has explicit direction from travishathaway (CONTRIBUTOR) and
  danyeaw (MEMBER): use `NOTICES_DECORATOR_DISPLAY_INTERVAL`. The plan comment says
  "wiring `NOTICES_DECORATOR_DISPLAY_INTERVAL` into `get_notice_response_from_cache()`...
  along the lines already agreed here." Engaged. No AI disclosure requirement in repo
  facts.

**Check rationale**

The `thread-aware` check, quoted exactly as it reads in `tools/plan-check/rubric.md`:

> Gate A: Pass when the thread has no explicit maintainer direction pointing at a specific
> file, approach, or action. Fail when a thread highlight from an owner, member, or
> contributor names a specific file or code location as the culprit or posts a patched
> artifact requesting testing, and the plan comment ignores or contradicts that direction.
> Gate B: Pass when the repo-facts block states no AI disclosure requirement. Fail when
> repo-facts states an explicit AI disclosure requirement (e.g. "all AI usage must be
> disclosed") and the plan comment does not disclose AI assistance. The check fails if
> either gate fails. Unclear on gate A: if it is genuinely ambiguous whether a thread
> comment is "explicit direction" or just a hypothesis, treat it as no direction (pass).
> Unclear on gate B: if the AI policy wording is ambiguous, treat as no requirement (pass).

It reads that way because I needed to distinguish two separate failure modes that both
show up in "thread and convention" rejects. `pkg-04` (fzf) fails gate A: the owner named
`src/tui/light_windows.go`, posted a patched binary, and the plan ignored that
direction entirely. `pkg-20` (ghostty) fails gate B: the repo requires disclosure of all
AI usage and the plan comment has none. The same check had to catch both cases without
conflating them — hence two named gates rather than a single vague "engages with thread"
condition.

The "explicit direction" qualifier on gate A is load-bearing. A maintainer posting a
hypothesis or a related workaround is not the same as pointing at the culprit file and
asking for testing. Without that qualifier, thread comments that are observations rather
than direction would incorrectly fail plans that had nothing to engage with.

**Trade-offs**

Gate A passes automatically when there is no explicit maintainer direction, which means
the check never looks at whether a plan comment is *well-written* for the thread — only
whether it contradicts or ignores direction that is already there. A plan comment that is
boilerplate or adds nothing to the thread but breaks no explicit direction still passes
gate A.

`pkg-03` (ripgrep) shows where this bites: the plan comment "engages the prior-art PR
instead of racing it" — that is good behavior the check rewards, but only because the
thread had direction to follow. A similarly well-written comment on a thread with no
maintainer comments earns the same pass for much less effort.

The trade is deliberate. A stricter gate A — "the comment must add something to the
thread" — would require the check to judge communication quality, which the rubric
explicitly rejects ("Judge the plan, not the polish"). A stricter gate would also diverge
from the gold label on `pkg-02`, which has zero thread comments and a terse comment that
says only what it will do, and is a clear accept. So the pass-on-no-direction rule is not
a gap; it is the correct behavior for a rubric that grades readiness, not eloquence.

Nothing else in the 20-package set changed because of gate A. The two thread-convention
rejects (`pkg-04`, `pkg-20`) both fail on specific explicit signals (patched binary,
stated disclosure policy), not on judgment calls. Every clear-accept has either no
maintainer direction (gate A passes trivially) or engages with it (gate A passes on
evidence). No package's verdict rested on the ambiguity clause.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
