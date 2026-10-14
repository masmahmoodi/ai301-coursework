# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/209

**Branch**

fix/62-redis-url

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

Two harness runs total: one 3-package smoke run and one full 20-package run on the Sonnet grader the harness pins.

1. Smoke run (3 packages, `--limit 3`, no `--save-run`): `agreement: 3/3 scored items` —
   pkg-01 (standards-wall) reject✓, pkg-02 (clear-accept) accept✓, pkg-03 (silent-drift)
   reject✓. Confirmed the tool was operational; did not count toward bar.
2. Full run (`--save-run /tmp/eval-run-unit4.txt`): `agreement: 20/20 scored items (bar:
   18/20: PASS)`, with `categories: clear-accept 7/7  not-tested 4/4  silent-drift 4/4
   standards-wall 2/2  unreviewable 3/3`. That is the run saved in `eval-run.txt`.

No `--only` runs were needed. The full run produced no disagreements, so there was nothing
to re-check. The rubric, evidence guide, and procedure were not revised between the smoke
and full runs — the full run is the same files run against all 20 packages.

**Package analysis**

`pkg-13` (`cabal/cabal#10782`). My rubric decided **accept**; the gold label is **accept**.
They agree.

`pkg-13` is a clear-accept that I chose because it demonstrates the honest-outcome rule:
the plan honestly discloses that the fix is scoped to one escape path (caret-escaping cmd
metacharacters), and the evidence shows the before/after behavior precisely. All four of
my rubric checks pass without friction:

- **plan-fidelity:** The diff touches only the one file the plan names
  (`Distribution/Simple/Program/Run.hs`), and the description's Changes section names that
  file. No mismatch between plan scope and diff. Pass condition met: every file in the diff
  falls inside the plan's stated boundary.

- **test-evidence:** The before state shows the specific failure (a shell metacharacter in a
  path breaks the command); the after state shows the fix produces the correct output. The
  evidence exercises the exact code path changed (the escaping function in the named file).
  Pass condition met: before/after artifacts are observable and specific to the changed path.

- **diff-quality:** The diff is a tightly bounded change — one function, no debug
  statements, no unrelated hunks, no commented-out code. The fix is immediately visible.
  Pass condition met: every hunk serves the plan's stated fix.

- **standards-comms:** All template sections filled with real content. The plan's Notes for
  Reviewers explicitly says the fix is scoped (does not handle all metacharacters, only
  caret) — an honest disclosure that the rubric's honest-outcome rule treats as ready, not
  a hold. AI disclosure not required by that repo. Pass condition met.

`pkg-13` also tests the honest-outcome rule explicitly: the plan acknowledges the fix
addresses only one metacharacter family. A rubric that equates "less than everything" with
"hold" would fail this package and miss the gold label. My rubric's verdict rule and the
honest-outcome language in the test-evidence check are what keeps `pkg-13` as an accept.

**Check rationale**

The `test-evidence` check, quoted exactly as it reads in `tools/pr-precheck/rubric.md`:

> Pass when the evidence includes: (a) a before-state artifact showing the failure the plan
> aimed to fix (error output, failing assertion, or wrong behavior), (b) an after-state
> artifact showing the corrected behavior on the same code path, and (c) visible outcomes
> for the repo's own required checks. Evidence must exercise the actual code path changed,
> not an unrelated path. A PR that honestly discloses that end-to-end evidence was
> impossible (with an explained substitute that covers the code path) passes this check.
> Fail when evidence is missing entirely, says only "tests pass" without output, or
> exercises a different code path than the change.

It reads that way because "tests pass" is the canonical not-tested tell: it looks like
evidence but conveys nothing observable. The pass condition requires three things because
they each catch a different failure mode: (a) the before-state catches PRs that prove
nothing failed in the first place; (b) the after-state on the same code path catches PRs
that fix one path and test a different one (the pkg-14 failure: evidence exercised a GET
with header casing, not the path the diff changed); (c) visible check outcomes catch PRs
that claim CI passed without showing it.

The honest-outcome clause was added explicitly to handle the DuplicateTableError deviation
in my own PR: the app cannot start end-to-end, but the before/after Python script and the
unit tests cover the actual probe code path. Without the clause, a rubric would either
fail my own PR on test-evidence (wrong) or require special-casing (fragile). The clause
is general: any disclosed substitution that covers the changed code path passes. The
boundary condition — "covers the code path" — is what prevents "I disclosed I couldn't
test it" from being a free pass.

**Trade-offs**

The `test-evidence` check's "same code path" condition can miss a case: if the plan names
a file but the evidence runs the fix through a different layer of the same file's logic
(e.g., an integration test that exercises the endpoint but not the specific line changed),
the check has to decide whether that "counts" as the same code path. The pass condition
says "the actual code path changed, not an unrelated path" — which is directionally
correct but leaves some judgment.

`pkg-07` shows where this is load-bearing: the evidence runs only the test suite for
module A, but the fix changes module B (a shared utility). The code paths are different
files, so the check correctly fails it. `pkg-14` is the cleaner case: the evidence
exercises a GET on the unchanged path (header casing), while the diff changes the POST
body parsing. Both fail the check correctly because the code path mismatch is clear.

The trade-off is: a PR that tests the right behavior via an integration layer rather than
the direct code path may fail this check even if the integration coverage is meaningful.
My rubric accepts the false negative there because the alternative — allowing "integration
tests pass" as sufficient evidence — is what makes not-tested packages look ready. The
honest-outcome clause is the escape hatch when end-to-end is genuinely impossible.

Nothing in the 20-package set changed because of this trade-off. Every not-tested reject
has a clearly different code path in its evidence (pkg-04, pkg-07, pkg-10, pkg-14), and
every clear-accept has evidence that exercises the changed file directly (pkg-02, pkg-05,
pkg-08, pkg-11, pkg-13, pkg-16, pkg-19).

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
