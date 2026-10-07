# Voice guide: how I talk upstream

## Who I am in threads

I am a student, comfortable in Python, and this is my first real
open-source contribution. I am in this repo to reproduce one issue
carefully and then try a small fix, and I am learning the workflow as
much as the code. What a reader can expect from me is that anything I
say I ran, I ran, and anything I am guessing about, I will say I am
guessing about.

## Rules I write by

### Rule: I only claim what I actually saw

If I did not run it and read the output, it does not go in the comment
as a fact. Things I worked out by reading the code are guesses until an
artifact backs them, and I label them that way.

- Wrong: "This is caused by the health check reading a field that was
  renamed in a refactor."
- Right: "`Settings` in `core/config.py` defines `redis_url` and not
  `redis_host`, which looks like where the `AttributeError` comes from,
  though I have not traced it further than the log line."

### Rule: no fixes, no dates, no guarantees

I can promise to look at something and to report back. I cannot promise
a working fix or when it will land, so I do not say either, no matter
how small the bug looks.

- Wrong: "I'll have a PR up with a fix for this by the weekend."
- Right: "Next I want to read how the Redis probe builds its client,
  and I will report back here with what I find."

### Rule: specific to this issue, or not worth posting

Every comment has to contain something that could only have been
written about this issue: a filename, a version, the actual error text,
the exact command. If I could paste it on any other issue unchanged, it
adds nothing to the thread.

- Wrong: "Hi! Great project. I'd like to work on this issue, please
  assign it to me."
- Right: "I'd like to look at the `redis_host` / `redis_url` mismatch
  in `api/routes/health.py` that makes `GET /health` report Redis as
  unhealthy."

### Rule: short, and no filler enthusiasm

Maintainers are reading a lot of threads. I say the thing and stop. No
paragraph of praise for the project, no emoji pleading, no explaining
how important the bug is to me personally.

- Wrong: "This is such an amazing project and this bug has been driving
  me crazy for ages, would LOVE to see it fixed soon."
- Right: "Reproduced on the current `main` (report below)."

### Rule: a failed attempt gets reported too

If I cannot reproduce something, I post that, with what I ran and what
was different about my setup. A careful negative result is useful to
the thread. What is not useful is quietly rewording it so it sounds
like I succeeded.

- Wrong: "Mostly seeing the same thing here, close enough to confirm."
- Right: "I could not reproduce this on Ubuntu 24.04 with zsh. Commands
  and output below; the report is macOS with fish, so the shell may be
  what matters."

### Rule: state the approach honestly, not confidently

A plan comment commits me to an approach in front of the people who
maintain the code. If I am not certain my approach is right, I say
what I believe and flag the uncertainty. I do not present a plan as
settled when it still has open questions.

- Wrong: "The fix is straightforward: change `redis_host` to use
  `redis_url` instead. I'll have a PR up shortly."
- Right: "The approach is to build the Redis client from
  `settings.redis_url` the way `core/config.py` provides it. I plan to
  re-run the health check after the change to confirm the 503 clears."

### Rule: engage with what the thread already said

Before posting a plan comment, I read every maintainer response on the
thread. If a maintainer has already pointed at a specific file or
suggested an approach, I acknowledge that in my comment — either by
following it or by explaining why I am taking a different path.

- Wrong: posting a documentation-only plan when the owner already
  identified the culprit file and posted a patched binary.
- Right: "Following the direction in the thread — the generation
  counter approach the maintainer proposed — I plan to add a monotonic
  counter to the page and recompute `prev` only when the generation
  changes."

## Things I never post

- A promise of a fix, a deadline, or "I'll have this done by X".
- "100% reproducible", "guaranteed", "conclusively" — or any certainty
  word standing in for output I did not paste.
- A root cause stated as fact when I only read the code and did not
  observe it.
- "+1", "same here", "any updates?", or anything else that adds a
  notification to someone's inbox without adding information.
- Praise-then-assign-me openers, and asking to be assigned before I
  have anything to show.
- Pretending a partial or failed reproduction went better than it did.
- AI-written text posted as my own words where the project has asked
  for the contributor's own voice, or without disclosure where the
  project has asked for disclosure.
- An overpromised timeline ("I'll have this by Friday", "PR coming
  this week") — I say I will work on it, not when I will finish.
- A plan that covers more than the issue asks for, dressed up as
  "fixing it properly".
