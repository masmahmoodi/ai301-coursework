# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

**Where it lives**: In an eval bundle, the plan-context block contains the scope pair
(the exact file paths the plan names plus the boundary statement such as "not in scope:
X") and the deviation notes. The candidate PR's diff section lists every changed file.
The PR description's Summary and Changes sections contain fidelity claims ("this PR
changes only api/routes/health.py"). In live mode: plan.md at the root of the working
copy contains the scope and deviation notes; `git diff main...HEAD` (three dots, from
the fork's working copy) produces the diff of every file the branch changes relative to
main; the draft PR description contains the fidelity claims.

**What good looks like**: Every file path that appears in the diff also appears in the
plan's stated scope OR a deviation note explicitly names it and explains why it was added
to the change ("pyproject.toml: removing the attr-defined suppression is required by
CONTRIBUTING.md because the attribute access the suppression covered is now gone").
Description claims match the diff exactly — not broader ("only health.py was changed"
when pyproject.toml was also changed) and not narrower (claiming no other changes when
the diff contains two files).

**Silent drift tells**: A file appears in the diff that the plan never named and no
deviation note accounts for. The description says "this PR only changes X" when the diff
also edits Y. The plan states a boundary ("not in scope: Z") and the diff touches Z
anyway with no note. The description claims a behavior the diff does not implement.

## Test evidence (harness category: not-tested)

**Where it lives**: In an eval bundle, the test-evidence section contains the before-state
artifact (what the failure looked like before the fix), the after-state artifact (what the
fix produces), and the outcomes of the repo's required checks. The plan-context block's
test plan names the specific observable behavior to verify and the method to verify it
(e.g., "re-run curl against /health and confirm redis shows healthy"). In live mode:
test_evidence.md in the fork's working copy contains the before/after artifacts and check
outputs; `make test-unit`, `make test-integration`, `make lint`, `make typecheck` produce
the check outcomes.

**What good looks like**: The before-state artifact names the failure specifically
(an error message, a wrong value, a stack trace, a failing assertion) — "AttributeError:
'Settings' object has no attribute 'redis_host'" is decisive; "Redis was unhealthy" is
not. The after-state artifact shows the corrected behavior on the same code path the fix
addresses — if the fix changes api/routes/health.py, the after evidence exercises that
file's probe logic, not a different file. Required repo check outcomes are visible
(pass/fail plus output, not just "I ran them"). A PR that discloses that end-to-end
evidence was impossible (explaining the unrelated blocker) and substitutes direct
code-path evidence covering the same logic still passes this check.

**Not-tested tells**: Evidence says "verified locally" or "tests pass" without showing
output. The after-state evidence exercises a GET on a different endpoint than the one the
fix addresses. The plan's test plan named a specific observable outcome ("GET /health
returns redis: healthy") and the evidence does not show that outcome. Required make
targets are left unchecked or their outcomes are not shown.

## Diff quality (harness category: unreviewable)

**Where it lives**: In an eval bundle, the diff section contains the unified diff (added
and removed lines for each changed file) and the commit list. In live mode: `git diff
main...HEAD` from the fork's working copy produces the full branch diff; `git log
main..HEAD` produces the commit list.

**What good looks like**: The fix is visible in the diff — the specific lines that
implement the plan's stated approach appear as added lines, and the lines they replace
appear as removed lines. Every hunk in every changed file serves the plan's stated fix or
is accounted for by a deviation note. No debug statements (print(), logging.debug(),
console.log()) appear in added lines that are not required by the fix. No commented-out
code blocks appear that were not there before. No large reformatting-only hunks that
change indentation or whitespace without changing logic.

**Unreviewable tells**: A debug print() appears in the diff in an added line. A block of
commented-out code appears that was not in the original file. A large hunk of reformatted
code (indentation changes, line-wrapping) surrounds the actual fix and makes the relevant
lines hard to identify. A file unrelated to the plan's scope is edited without a deviation
note explaining why.

## Standards and comms (harness category: standards-wall)

**Where it lives**: In an eval bundle, the repo-facts block contains the PR template
sections (each heading and its expected content), any contributing instructions, and the
stated policy on AI-use disclosure. In live mode: `.github/PULL_REQUEST_TEMPLATE.md` in
the working copy defines the template sections; `CONTRIBUTING.md` states any AI-use
disclosure requirement; the draft PR description in pr_draft.md contains the filled
sections; the issue thread contains any explicit maintainer direction.

**What good looks like**: Every section in the PR template (Summary, Issue, Changes,
Testing, Notes for Reviewers) contains real content — not just the HTML comment
placeholder. "Real content" means prose or bullets that a reviewer could act on: "Closes
#62" in Issue is real; an empty Issue section is not. The AI disclosure statement
("This PR was developed with AI assistance") appears in Notes for Reviewers where
CONTRIBUTING.md requires it. Any explicit maintainer direction in the thread (a named
file, a suggested approach, a posted patch asking for testing) is acknowledged in the
description — followed, or explicitly declined with a reason.

**Standards-wall tells**: A section still contains only its HTML comment placeholder text
(e.g., `<!-- One paragraph describing what this PR does and why -->`). The Notes for
Reviewers section is blank or contains only placeholder text when the repo requires AI
disclosure. A maintainer posted a specific patched file in the thread and the PR
description makes no reference to it. The Testing checklist has every box unchecked with
no explanation.
