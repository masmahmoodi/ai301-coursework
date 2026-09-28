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

masmahmoodi

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62#issuecomment-5863368277

Posted by masmahmoodi at 2026-09-28T04:28:53Z. Exact text as posted:

```
I'd like to take issue #62 as my first contribution here.

The area I'm looking at is the Redis probe in `api/routes/health.py`, which builds
its client from `settings.redis_host` / `settings.redis_port`, while `Settings` in
`core/config.py` defines `redis_url` instead. I've set the project up locally per
`docs/SETUP.md` and have been investigating that path.

I'll post my detailed reproduction report next, with the exact commands, the
environment I ran on, and the response and log output I observed.
```

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62#issuecomment-5863374128

Posted by masmahmoodi at 2026-09-28T04:29:25Z, after the claim comment. Every command
and output line below came from a real run on this machine. Exact text as posted:

````
Reproduced on `main` at `f89c06f`. `GET /health` returns 503 with
`"redis": "unhealthy"` while Redis is up and reachable, and the log line is an
`AttributeError` for `redis_host`, matching the report.

**Environment**

- macOS 14.7.6 (x86_64), Python 3.13.7, Docker 29.1.5
- Repo at `main` / `f89c06f`, `.env` copied from `.env.example`
- Backing services from the repo's `docker-compose.yml`: `redis:7-alpine`,
  `postgres:16-alpine`, `chromadb/chroma:0.4.22`
- Installed packages: redis 8.1.0, fastapi 0.141.1, uvicorn 0.54.0,
  sqlalchemy 2.1.1, pydantic-settings 2.15.0, structlog 26.1.0

Two deviations from `docs/SETUP.md`, neither of which touches the Redis probe:

1. `make setup` failed for me building a `libcst` wheel on Python 3.13 (it comes
   in transitively via `mutmut` in the `dev` extra). I installed runtime
   dependencies only, with `.venv/bin/python -m pip install -e .`, then ran the
   documented `alembic upgrade head` and `scripts/seed_db.py` steps, which both
   succeeded.
2. Another container on my machine already held port 6379, so I changed the
   Redis host port mapping in `docker-compose.yml` to `6380:6379` and set
   `REDIS_URL=redis://localhost:6380/0` in `.env` — the option the SETUP
   troubleshooting section suggests. The health probe never reads `REDIS_URL`,
   so this does not affect the result.

**Steps**

```
$ cp .env.example .env
$ docker compose up -d
$ .venv/bin/python -m pip install -e .
$ .venv/bin/alembic upgrade head
$ .venv/bin/python scripts/seed_db.py
$ .venv/bin/uvicorn api.main:app --host 127.0.0.1 --port 8000
```

First, confirming Redis is actually up, so that "unhealthy" cannot be a
connection problem:

```
$ docker compose exec -T redis redis-cli ping
PONG
```

and from the app's own configuration:

```
$ .venv/bin/python -c "
import redis
from core.config import settings
print('settings.redis_url =', settings.redis_url)
print('ping ->', redis.Redis.from_url(settings.redis_url, decode_responses=True).ping())
print('hasattr redis_host ->', hasattr(settings, 'redis_host'))
print('hasattr redis_port ->', hasattr(settings, 'redis_port'))
"
settings.redis_url = redis://localhost:6380/0
ping -> True
hasattr redis_host -> False
hasattr redis_port -> False
```

Then the health endpoint:

```
$ curl -s -w '\nHTTP_STATUS=%{http_code}\n' http://127.0.0.1:8000/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-09-28T03:29:19.210950"}}
HTTP_STATUS=503
```

**Observed**

The server log for that same request:

```
2026-09-27 20:29:19 [error    ] postgres_health_check_failed   error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')" request_id=554aa109-8aaf-4c24-8f62-5afde95b0a74
2026-09-27 20:29:19 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'" request_id=554aa109-8aaf-4c24-8f62-5afde95b0a74
2026-09-27 20:29:19 [debug    ] vector_db_health_check_passed  request_id=554aa109-8aaf-4c24-8f62-5afde95b0a74
```

So the Redis branch fails with `'Settings' object has no attribute 'redis_host'`
even though the ping above succeeds against the same Redis.

**Expected**

`GET /health` reports `"redis": "healthy"` and returns 200, since Redis is
running and reachable at the configured URL.

**One thing that is not this issue:** `postgres` also reports unhealthy in the
body above, with a separate SQLAlchemy 2.x error about a textual SQL expression.
That looks like issue #61 rather than this one, so I left it alone; I am
mentioning it only because it shares the response body and is why the overall
status is unhealthy too.

I have not changed any code — this is reproduction only.
````

## Eval iterations

**Run history**

Two runs, both full 20-package runs on the Sonnet grader the harness pins.

1. Run 1 (first try, `--out u2run1.json`, no `--save-run`): `agreement: 20/20 scored
   items  (bar: 18/20: PASS)`, with `categories: clear-accept 8/8  disclosure 1/1
   no-evidence 4/4  unfollowable-comms 3/3  wrong-target 4/4`.
2. Run 2 (final, `--save-run eval-run.txt`): `agreement: 20/20 scored items  (bar:
   18/20: PASS)`, same category line. That is the run saved in `eval-run.txt`.

I never used `--only`. It is the flag for re-checking packages the rubric got wrong,
and run 1 did not get any wrong, so spending about $0.20 a package to re-confirm
answers I already had would have been a waste. The README's canary rule did not apply
either: canaries are for a revision that loosens a check, and I did not revise the
rubric at all between the two runs — run 2 is the same files run again to produce the
saved transcript. The `rubric.md sha256:5927b5e09c0bd0aa` and
`evidence-guide.md sha256:17d82e4be1ff6150` in the `eval-run.txt` header match the
files now in `tools/repro-check/`.

What I did instead of a revise loop was read run 1's per-check JSON to confirm each
reject came from the check I meant it to come from. It did: the four `no-evidence`
packages failed `artifact-shows-issue-behavior`, the `wrong-target` packages failed on
artifact or deviation, the three `unfollowable-comms` packages each failed a different
one (`pkg-06` on `env-recorded`, `pkg-18` on `steps-rerunnable`, `pkg-19` on
`claim-comment-specific`), and `pkg-20` failed `ai-disclosure` alone. On the eight
accepted packages nothing failed except `control-run-present`, which is `preferred`
and cannot move a verdict.

**Package analysis**

`pkg-16` (`pandas-dev/pandas#66656`). My rubric said **reject**; the gold label is
**reject**. They agree, and this is the package that made me write a separate check
for version deviation instead of folding it into the artifact check.

What makes `pkg-16` hard is that its artifact is genuinely on target. The report runs
the issue's own snippet and shows the issue's own error:

```
>>> df.reset_index().rename_axis((1, 2, 3))
ValueError: Length of new names must be 1, got 3
```

That is the crash the issue describes, so `artifact-shows-issue-behavior` passed. What
fails is the environment it was produced in. The report's environment line reads
"pandas 1.5.3 (pip), Python 3.10.12, Ubuntu 22.04 (x86_64)", while the issue states
the reporter "confirmed the bug on the latest version and on the main branch", and a
commenter confirms it "on pandas 2.3.3 and current main". So the run is two major
versions behind the issue's target, and nothing in the report mentions it.

That is exactly the condition `deviation-named` fails on, and it was the only required
check that failed for this package. The ValueError on 1.5.3 is that old version's
behavior; it is not evidence about the bug the issue is actually about, and a
maintainer reading it would be misled into thinking the report confirmed something it
did not. The contrast that settled the threshold for me is `pkg-03`, `pkg-07`,
`pkg-11` and `pkg-12`, which all run on a different version than the issue and all
pass, because each one says so in a sentence — `pkg-03`'s "The issue was filed against
13.0.0; behavior is unchanged on 15.2.0" is the whole difference between them and
`pkg-16`.

**Check rationale**

The `deviation-named` check, quoted as it currently reads in
`tools/repro-check/rubric.md` (the Pass condition column):

> Pass when the report ran on the issue's target or on a newer or current release, or
> when the report ran somewhere different and says so in words, naming what differed.
> One sentence is enough: naming the delta is the whole requirement. Fail when the
> report ran on an older version, a different operating system, a different install
> method, or a different build profile than the issue's stated target and never
> mentions it, because a silent deviation turns the artifact into evidence about a
> different world than the one the issue is about.

It reads that way because my first instinct was wrong. I started out planning to
handle version mismatches inside `artifact-shows-issue-behavior` — if you tested the
wrong version, the artifact is off target. But that collapses two different failures
into one check, and it would have rejected half the accepted packages: running on a
newer release than the issue is normal and often the most useful thing a reproducer
can do, since it tells the maintainer the bug survived. So the question is not whether
the versions match, it is whether a mismatch is disclosed.

That is why the pass condition is written around the sentence rather than the version
numbers. "One sentence is enough" is doing real work: it sets the bar at disclosure,
not at matching, so `pkg-11` reproducing on macOS when the issue says Linux passes
purely on its closing line "Matches the report on a different OS and install method".
I also deliberately listed the axes — version, OS, install method, build profile —
because `pkg-17` deviates on build profile rather than version, and an earlier
version-only wording would have missed that axis entirely.

**Trade-offs**

`deviation-named` only asks whether a difference was named, not whether the reproducer
was right about it mattering. That is the trade, and it buys a specific kind of false
accept: a report can deviate badly, say so in one throwaway sentence, and pass this
check. `pkg-17` is the package where I can watch it happen. It runs the Store release
against an issue filed on a git-main build, and it does name the difference — then
draws the wrong conclusion from it, claiming the reproduction "demonstrates that the
problem is not limited to git-main builds" when its artifact shows garbled output with
the terminal still alive rather than the reported crash. `deviation-named` passed
`pkg-17`. It was rejected by `artifact-shows-issue-behavior` and
`outcome-stated-honestly` instead, which is the intended division of labour, but it
shows that if the artifact had happened to be on target, a badly-reasoned deviation
would have sailed through on a single sentence.

I took that trade because the opposite rule is worse. A check that required the
version to match would reject `pkg-03`, `pkg-07`, `pkg-11` and `pkg-12` — half the
clear-accepts — and would punish exactly the reproductions that are most useful to a
maintainer. A disclosure bar is checkable by someone else and gives the maintainer
what they need to judge the deviation themselves; a matching bar is not even
well-defined when an issue says "latest and main".

Nothing else in the set changed because of it, and here is how I know. Both full runs
produced the same 20/20 and the same per-package table. In run 1's per-check JSON,
`deviation-named` fails on exactly three packages: `pkg-16`, where it is the only
required failure and therefore the check that decides the verdict; `pkg-06`, which was
already failing `env-recorded` because it has no environment record at all; and
`pkg-14`, which was already failing on its setup-only artifact. On the eight accepted
packages it passes every time. So `pkg-16` is the single package whose verdict rests
on this check, which is also why I chose it for the package analysis above.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
