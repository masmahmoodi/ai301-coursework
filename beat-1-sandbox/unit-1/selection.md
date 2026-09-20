# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62

**Verdict output**

````
**issue-select — live mode, 3 candidates, run 2026-09-20**

Scope confirmed: all three URLs are in `codepath/pathreview-ai301-fa26-s1`, the repo
named in `scope.md`. Path Review house rule applied: classmates' claim comments do not
block an issue here. (No claim comments appear on any of the three regardless.)

Shared repo evidence (github.com, gathered 2026-09-20): repo line `archived: false`,
1 star, `pushed_at 2026-09-16`, no releases published, 71 open issues; last 5
default-branch commits all authored by the human account `Aburke225`, newest
2026-09-16; maintainer first-response sample — #52 opened 2026-09-10, first
COLLABORATOR comment 2026-09-16 (6.0 days); #43 opened 2026-09-10, first COLLABORATOR
comment 2026-09-16 (6.0 days); #69 opened 2026-09-10, no maintainer comment yet;
contribution policy — `docs/CONTRIBUTING.md` exists and states nothing about AI or
contribution tooling, no `AI_POLICY.md`, and `.github/PULL_REQUEST_TEMPLATE.md` has no
AI-disclosure checkbox. The repository has zero pull requests, open or closed, so no
candidate can carry a linked PR.

**Accepted, in fit order**

1. **#62 — Health check references `settings.redis_host`, which does not exist on
   Settings.** Fit: single-file Python fix in `api/routes/health.py` against a field
   that already exists (`settings.redis_url`), with the wrong behaviour and the log
   line both written down — the smallest, most fully-specified backend bug of the
   three, and the profile asks for exactly that. CONTRIBUTING even names this issue as
   the owner of the `attr-defined` suppression in `pyproject.toml`, so "done" is
   unambiguous.
2. **#53 — PII scrubber fails to redact parenthesized US phone numbers.** Fit: also
   one-file Python (`safety/pii_scrubber.py`), with a runnable reproduction and four
   named covering tests, but the fix is a regex change whose edge cases are easier to
   get subtly wrong than a renamed settings field.
3. **#68 — Keyword search raises `ZeroDivisionError` when the index is empty.** Fit:
   still a bounded Python fix, but the issue's own "Estimated effort: 2–4 hours" is the
   largest of the three and the fix reaches into how `rank-bm25` is called, one layer
   further from the repo's own code than the other two.

**Rejected:** none.

Per-check read-out:

| Check | #62 | #53 | #68 |
|---|---|---|---|
| maintainer-alive | pass | pass | pass |
| repo-in-use | pass | pass | pass |
| newcomer-scope | pass | pass | pass |
| unclaimed | pass | pass | pass |
| ai-contribution-policy | pass | pass | pass |
| maintainer-responsive *(preferred)* | pass | pass | pass |
| newcomer-signposting *(preferred)* | pass | pass | pass |
| adoption *(preferred)* | fail | fail | fail |

The one preferred failure is shared and does not separate the candidates: 1 star and no
published release is what a ten-day-old course repo looks like, not a sign of disuse
— `repo-in-use` already carried that judgement on the push date.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/62",
    "checks": [
      {"name": "maintainer-alive", "grade": "pass", "evidence": "All 5 newest default-branch commits are authored by the human account Aburke225, newest 2026-09-16, 4 days before today"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "Repo is archived: false and last push to any branch is 2026-09-16, well inside 180 days"},
      {"name": "newcomer-scope", "grade": "pass", "evidence": "One defect in one file: 'builds its client from settings.redis_host ... it carries a single redis_url, which the probe should use instead', with steps to reproduce and the expected 503 body"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; the repository contains zero pull requests so no linked PR exists; 0 comments on the issue"},
      {"name": "ai-contribution-policy", "grade": "pass", "evidence": "docs/CONTRIBUTING.md states no policy on AI or contribution tooling, and the PR template carries no AI-disclosure requirement"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Issue #52 opened 2026-09-10 drew its first COLLABORATOR comment on 2026-09-16, 6.0 days, inside the 7-day threshold"},
      {"name": "newcomer-signposting", "grade": "pass", "evidence": "Labels include 'good first issue' and 'tier-1', and the body names api/routes/health.py and core/config.py as the files to touch"},
      {"name": "adoption", "grade": "fail", "evidence": "1 star, under the 50-star threshold, and no release has ever been published"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/53",
    "checks": [
      {"name": "maintainer-alive", "grade": "pass", "evidence": "All 5 newest default-branch commits are authored by the human account Aburke225, newest 2026-09-16, 4 days before today"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "Repo is archived: false and last push to any branch is 2026-09-16, well inside 180 days"},
      {"name": "newcomer-scope", "grade": "pass", "evidence": "One defect in one pattern: 'matches dashed formats like 555-123-4567 but not the parenthesized format (555) 123-4567', with a runnable reproduction and four named covering tests"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; 0 comments; the repository contains zero pull requests so no linked PR exists"},
      {"name": "ai-contribution-policy", "grade": "pass", "evidence": "docs/CONTRIBUTING.md states no policy on AI or contribution tooling, and the PR template carries no AI-disclosure requirement"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Issue #52 opened 2026-09-10 drew its first COLLABORATOR comment on 2026-09-16, 6.0 days, inside the 7-day threshold"},
      {"name": "newcomer-signposting", "grade": "pass", "evidence": "Labels include 'good first issue' and 'tier-1', and the body supplies a copy-pasteable reproduction with observed output"},
      {"name": "adoption", "grade": "fail", "evidence": "1 star, under the 50-star threshold, and no release has ever been published"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/68",
    "checks": [
      {"name": "maintainer-alive", "grade": "pass", "evidence": "All 5 newest default-branch commits are authored by the human account Aburke225, newest 2026-09-16, 4 days before today"},
      {"name": "repo-in-use", "grade": "pass", "evidence": "Repo is archived: false and last push to any branch is 2026-09-16, well inside 180 days"},
      {"name": "newcomer-scope", "grade": "pass", "evidence": "One defect with a named cause and a stated fix boundary: 'index([]) raises ZeroDivisionError inside the rank-bm25 library ... index() shouldn't raise on an empty corpus either', plus a Relevant files list"},
      {"name": "unclaimed", "grade": "pass", "evidence": "assignees: none; 0 comments; the repository contains zero pull requests so no linked PR exists"},
      {"name": "ai-contribution-policy", "grade": "pass", "evidence": "docs/CONTRIBUTING.md states no policy on AI or contribution tooling, and the PR template carries no AI-disclosure requirement"},
      {"name": "maintainer-responsive", "grade": "pass", "evidence": "Issue #52 opened 2026-09-10 drew its first COLLABORATOR comment on 2026-09-16, 6.0 days, inside the 7-day threshold"},
      {"name": "newcomer-signposting", "grade": "pass", "evidence": "Labels include 'good first issue' and 'tier-1', and the body lists Relevant files plus an estimated effort of 2-4 hours"},
      {"name": "adoption", "grade": "fail", "evidence": "1 star, under the 50-star threshold, and no release has ever been published"}
    ],
    "verdict": "accept"
  }
]
```
````

---

## Eval iterations

**Run history**

Two runs, both full 20-issue runs, both on the Sonnet grader the harness pins.

1. Run 1 (first try, `--out run1.json`, no `--save-run`): `agreement: 20/20 scored
   items  (bar: 18/20: PASS)`, with `categories: claimed 4/4  clear-accept 8/8
   dead-repo 3/3  policy 1/1  scope 4/4`.
2. Run 2 (final, `--save-run eval-run.txt`): `agreement: 20/20 scored items  (bar:
   18/20: PASS)`, same category line. That is the run saved in `eval-run.txt`.

I never used `--only`. It is there for re-checking issues the rubric got wrong, and run
1 did not get any wrong, so spending about $0.20 an issue to re-confirm answers I
already had would have been a waste. Instead of a revise loop I opened run 1's
per-check JSON and made sure each reject came from the check I meant it to come from,
not from luck: all three `dead-repo` issues failed `maintainer-alive` and
`repo-in-use`, all four `claimed` issues failed `unclaimed`, all four `scope` issues
failed `newcomer-scope`, and `issue-12` failed only `ai-contribution-policy`. On the
eight accepted issues, nothing failed except `preferred` checks, which the verdict rule
ignores.

I did not change the rubric between run 1 and run 2 — I ran the same file again to get
the saved copy. The `rubric.md sha256:9de24ea018bd1029` in the `eval-run.txt` header is
the same file that is in `tools/issue-select/` now.

**Issue analysis**

`issue-09` (`conda/conda#7617`, "conda config clear option"). My rubric said
**accept** and the gold label is **accept**, so they agree. What I want to explain is
why, because two things about this issue look at first like reasons to reject it.

The first is that it is old and somebody already asked for it. The thread has
`MesaJonathan (NONE) on 2022-01-20` saying "I'd like to take a swing at this as my
first open-source contribution. Does it need to be assigned to me?", and the issue was
opened `2018-08-03`. That is a real claim comment. But my `unclaimed` check only blocks
on a claim that "is dated within 90 days of capture", and this one is over four years
before the `2026-08-05` capture date, so the stale part of the check applied and it did
not block. The maintainer answered the same day — `jakirkham (MEMBER)`: "Think you can
just give it a try if you are interested" — which is a maintainer opening the issue up,
not handing it to one person, so the other fail condition did not apply either.

The second is the linked PR. The repo facts say `linked PRs: conda/conda#11627
(closed)`. My check cares about the PR's state: "A closed-unmerged linked PR is an
abandoned attempt, not a live claim". So a closed PR tells me the issue is free again,
not that somebody has it. The run's own evidence line says the same thing: "No
assignee; linked PR #11627 is closed (abandoned attempt); the sole claim comment
(2022-01-20) is far older than 90 days before the 2026-08-05 capture."

`newcomer-scope` passed for a different reason than the body being detailed, because it
is not — the body is three lines. It passed because this is a feature request, and my
check requires a maintainer to have backed a feature: the opener `jakirkham` is a
`MEMBER` and the issue carries the `good first issue` label. The only check that failed
was `maintainer-responsive`, and that one is `preferred`, so it cannot change the
verdict. It is a heads-up that conda is slow to reply, not a reason to skip an issue
that is alive, unclaimed and small.

**Check rationale**

The `unclaimed` check, quoted as it currently stands in `tools/issue-select/rubric.md`
(the Pass condition column):

> Fail if any of these holds: an assignee is named; a linked PR is open and does the
> work this issue asks for; a maintainer named one person as the one doing it or said
> other contributions are not wanted; or a claim comment ("working on this", "I would
> like to work on this", "can I pick this up") is dated within 90 days of capture and
> no maintainer has since released the issue. Pass otherwise. A claim older than 90
> days with no open linked PR is stale and does not block, especially where a
> maintainer invited anyone to try. A closed-unmerged linked PR is an abandoned
> attempt, not a live claim. Only a maintainer releases a claim, by unassigning or by
> saying the issue is open again: an automated inactivity or stale-bot nudge asking the
> claimant to confirm is not a release, and the claim stands until the bot actually
> unassigns. A good-first-issue label says the issue is friendly, never that it is
> free.

I wrote it this way because "is someone already on this?" is not one signal, it is
four, and they often disagree with each other. If the check only looked at
`assignees:`, it would pass `issue-18`, which has no assignee but eight different
people in the thread asking to take it or saying they already started. If it only
looked at `linked PRs:` without checking each PR's state, it would fail `issue-09`,
whose only linked PR is closed. And if it treated every claim comment as blocking, it
would fail `issue-09` over a comment from 2022 and `issue-12` over one from 2024. That
is what the 90-day window is for: somebody saying they would do something four years
ago does not tell me anything about now.

The sentence about stale bots is in there because of cases like `issue-08`. That issue
has `assignees: piyushagarwal-55` and an open linked PR, and then `zulipbot (MEMBER)`
posts "we'd appreciate a quick `@zulipbot abandon` comment so that someone else can
claim this issue... you will be automatically unassigned in 4 days." Read quickly, that
sounds like the issue is being freed up. It is not — it is a warning that it might be
in four days, and the assignee field still has a name in it. So I made the check care
about who actually releases a claim instead of how the comment sounds.

**Trade-offs**

The 90-day window is the part of this check where I know I am giving something up, and
what I risk is accepting an issue somebody else is on, not rejecting a good one. If
someone commented "working on this" 100 days ago, never opened a PR, and is still
quietly working on it, my rubric reads that issue as free and I would end up
duplicating their work. I took that trade because the other mistake is worse for a
first contribution: if every old comment counts as a live claim, then old friendly
issues can never be taken by anybody, and those are a lot of what is actually available
to a beginner. 90 days is my guess at when a silent claim stops being real, and it is
the first number I would change if I started landing on issues someone else was halfway
through.

Nothing else in the set changed because of it, and here is how I know. Both full runs
gave the same 20/20 and the same per-issue table. Looking at run 1's per-check JSON,
`issue-09` is the only accepted issue where the window does real work — it is the one
accept that would flip to reject without it. `issue-12` also had a stale claim let
through (`jrings` in 2024), but it was rejected by `ai-contribution-policy` anyway, so
the window did not decide that one. Going the other way, `issue-18`'s newest claim is
`vishnukumar650` on `2026-08-02`, three days before capture, which is inside the window
and correctly blocked it. All four `claimed` issues failed `unclaimed` and all eight
`clear-accept` issues passed it.

---

## Selection rationale

**Selection rationale**

1. **Fit to my interests and the time available.** #62 is Python, backend, and one
   file. `api/routes/health.py` reads `settings.redis_host` and `settings.redis_port`,
   but the `Settings` class in `core/config.py` only defines `redis_url`. That is the
   kind of bug I want to start with: it is small enough that I can keep the whole thing
   in my head, and the right value already exists in the code, so I am not making a
   design decision. I should be able to check it by calling `GET /health` with Redis
   running and seeing the 503 go away. It is labelled `tier-1`, and the repo's
   CONTRIBUTING mentions it by name ("`api/routes/health.py` `attr-defined` is issue
   #62"), so I also know the fix includes removing that suppression from
   `pyproject.toml` and I am not guessing what a finished PR looks like. That feels
   doable in one sitting plus time to get CI green. It also lines up with what I want
   to get better at: APIs, backend code, and the actual contribution workflow.

2. **What the verdict got right, and what I had to judge myself.** The rubric was right
   about everything it can actually measure: the repo is alive (five commits from a
   real person, the newest four days ago), nobody is on the issue (no assignee, no
   comments, and the repo has zero pull requests at all), the scope is one bug with
   steps to reproduce, and there is no AI policy that would rule out how I work. It was
   also "right" in a way I had to look past — `adoption` failed on all three
   candidates, because 1 star and no releases looks dead by that measure, when really
   it is a course repo that is ten days old. That is exactly why I made `adoption`
   `preferred` instead of `required`. What the rubric could not do was pick between the
   three, since all of them passed every required check the same way. I chose #62 over
   #53 and #68 for reasons the rubric does not measure: fixing a wrong attribute name
   is simpler and harder to get wrong than writing a new regex case (#53) or adding a
   guard around a third-party BM25 call (#68), and for my first PR I would rather spend
   my energy on learning the workflow than on the code.

3. **How hard I expect claiming it to be.** Claiming it should be easy; getting CI
   green might not be. The issue has no assignee and no comments, and the Path Review
   house rule in `scope.md` says classmates' claims do not block me anyway, so
   commenting on it in Unit 2 should be straightforward. It does have the
   `good first issue` and `tier-1` labels, so it is one of the more obvious picks out
   of 71 open issues and someone else in my section might take it too — but the house
   rule says that is fine, since credit comes from the PR I open. The part I actually
   expect to slow me down is in CONTRIBUTING: a first PR from a fork "will sit at
   'waiting for approval to run workflows' until a maintainer releases it", and all
   five CI jobs have to pass before review. The maintainer has been replying in about
   six days, so I am planning for a wait rather than a same-day merge.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
