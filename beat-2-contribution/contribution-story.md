# Contribution: Longhorn-Developers/UT-Registration-Plus — Highlight Courses on UTRP Map

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** Masoud Mahmoodi
**GitHub Username:** masmahmoodi
**Issue:** https://github.com/Longhorn-Developers/UT-Registration-Plus/issues/864
**Pull Request:** https://github.com/Longhorn-Developers/UT-Registration-Plus/pull/1081

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Searched and evaluated real open-source issue candidates**

I used the CodePath AI301 Issue Selection site to search for issues matching my background in TypeScript, JavaScript, and backend development. I filtered for `good first issue`, `bug`, and `improvement` labels, then inspected several candidates. The main problem I encountered was that issues could appear open and available even when they were already closed, merged, or blocked by an unresolved prerequisite. The issue-select rubric caught two of these problems explicitly: one candidate (HHS/simpler-grants-gov #6599) returned REJECT because it was blocked by an open prerequisite issue with acceptance criteria marked "TBD" and had been deprioritized by the maintainer; a second candidate (LibreSign/libresign #9473) was already fully closed — PR #9483 had been merged on November 15, 2026, two weeks before I evaluated it. After these rejections I evaluated UT-Registration-Plus #864 ("Highlight Courses on UTRP Map") and confirmed it was SAFE TO CLAIM: open, unassigned, no competing open PRs, no human claim comments, and a previous draft PR (#868) that had been abandoned and closed on September 10, 2026 without merge. I updated my selection checklist to include verifying closed state, merge status of any linked PRs, and open dependency issues before treating a candidate as available.

**Unit 6: Drafted claim comment, investigated codebase, implemented feature, and ran tests**

I posted a claim comment at https://github.com/Longhorn-Developers/UT-Registration-Plus/issues/864#issuecomment-7153678976 (November 25, 2026). I then cloned the repository to `C:\Users\User\Desktop\UT-Registration-Plus`, read the full source of `Map.tsx` (256 lines) and `CampusMap.tsx` (821 lines), and implemented the feature on branch `feat/highlight-enrolled-buildings` (commit `cbf1be6`). The key discovery from reading the actual source: `ProcessInPersonMeetings.location` is typed as `CourseMeeting['location']` — an object with `.building` and `.room` fields — and `CampusMap` already uses `location?.building` internally for the daily path feature. I added `extractEnrolledBuildings` as a pure function in `utils.ts`, called it in `Map.tsx` via `useMemo`, and passed the result to `CampusMap` as a new `highlightedBuildings?: ReadonlySet<string>` prop. In `CampusMap`, enrolled buildings are added to `importantBuildings` (so they always render regardless of zoom or clustering) and their SVG circles get fill `#F8971F` (UT gold). I wrote 5 vitest unit tests for `extractEnrolledBuildings`; all 5 passed. TypeScript type check passed with zero errors. Biome linter passed with zero errors (3 pre-existing warnings in files I did not touch).

**Unit 7: Applied plan-check and pr-precheck rubric criteria manually; no executable tool was invoked**

I created `plan.md`, `plan-comment.md`, `pr_draft.md`, `test_evidence.md`, and `ship-manifest.md` in the fork's working directory. I then manually evaluated the plan-check criteria (5 required checks) against `plan.md` and the pr-precheck criteria (4 required checks) against the diff, `pr_draft.md`, and `test_evidence.md`, recording each result in `ship-manifest.md`. **No executable `plan-check` or `pr-precheck` tool was run** — no such programs exist at `~/.claude` or anywhere else on this machine; the rubric criteria were applied by reading the AI301 course rubric descriptions and evaluating each criterion by hand. All 5 plan-check criteria were satisfied (diagnosis grounded in source: all buildings get `#BF5700` with no enrollment check; scope bounded: 3 source files + 1 test file, all in the map module; plan names concrete files and functions; test plan specifies `pnpm test --run extractEnrolledBuildings → 5 passed`; no maintainer direction ignored). All 4 pr-precheck criteria were satisfied (diff's 4 files match plan scope; test evidence covers the code path; no debug statements or unrelated edits; no PR template or AI disclosure requirement). The manual evaluation produced ACCEPT verdicts for both. I did this after the implementation was done; next time I would apply the rubric before writing the first line of code.

**Unit 8: Inspected PR #1081 via GitHub API; pr-monitor tool does not exist**

I inspected PR #1081 at https://github.com/Longhorn-Developers/UT-Registration-Plus/pull/1081 on November 25, 2026 by making direct GitHub REST API calls. State: open. Base: `Longhorn-Developers/UT-Registration-Plus:main`. Head: `masmahmoodi:feat/highlight-enrolled-buildings`. Reviews: 0. Review comments: 0. The only comment is an automated one from `github-actions[bot]` notifying the Discord server of a new PR (the repo auto-posts status changes to their Discord). The GitHub API's `mergeable_state` field is `"unstable"`, which indicates at least one CI check is running or not passing; the check-runs API returned no data for commit `cbf1be6` (the endpoint returned 404 for the short SHA, and no legacy commit statuses were registered). I could not determine which specific CI jobs exist or whether they are failing vs. still running. No maintainer feedback has been received. **`~/.claude/pr-monitor/monitor.py` does not exist on this machine** — `python monitor.py --state` was not run; there is no such script. PR status: awaiting review.

---

## Why I Chose This Issue

I found issue #864 on the CodePath AI301 Issue Selection site while searching for TypeScript/JavaScript issues. The issue is titled "Highlight Courses on UTRP Map" and asks for a visual feature on the UT Registration Plus Chrome extension's campus map: courses a student has selected in their schedule should be highlighted in a different color or shade on the map, so they can quickly see which buildings they need to visit. The scope is well-defined — the map already exists, the schedule data is already in state, and the task is connecting the two — which fits the beginner contribution profile I was looking for.

I confirmed the issue was available by running the issue-select rubric. All five required checks passed: the repo is active (last commit within days), no one is assigned, no competing open or draft PRs exist, no human has posted a claim comment (the only comment was from `linear[bot]`, a project management automation), and the repo has no CONTRIBUTING.md, which means no stated restriction on AI-assisted contributions. A previous attempt (PR #868, by arunbagavathiannan) was closed without merge on September 10, 2026, and was in a dirty merge state at close. I evaluated it as an abandoned attempt, not a live competing claim.

I posted a claim comment on November 25, 2026:
https://github.com/Longhorn-Developers/UT-Registration-Plus/issues/864#issuecomment-7153678976

**Toolkit**

- Tool: issue-scout (the rubric-driven grading tool at `~/.claude/skills/issue-scout/`), used to evaluate UT-Registration-Plus #864 against the five required checks in `scope.md` and `rubric.md`
- What it produced: a SAFE TO CLAIM verdict with evidence; full output saved to `scan-output.md` and `scan-notes.md`
- Difficulties: two earlier candidates had to be rejected before this one; the rubric correctly caught a blocked issue (HHS #6599, newcomer-scope fail) and an already-merged issue (LibreSign #9473, state: closed); those rejections cost time but prevented working on a dead end
- What changed: I added a step to verify the closed/merged state of any linked PRs before treating an issue as available; I also check whether any prerequisite issues are open before selecting an issue

---

## Understanding the Issue

**Problem Description**

The UT Registration Plus Chrome extension includes a campus map that shows UT Austin buildings as colored circles on an SVG overlay over a campus image. Students can view the map alongside their selected schedule. Currently, all buildings render identically — every building marker uses the same burnt orange color (`#BF5700`) regardless of whether the student has a class there. A student looking at the map cannot immediately tell which buildings their enrolled courses meet in.

**Expected Behavior**

Buildings that contain rooms for courses in the student's active schedule should be visually distinguished from other buildings. The issue body says: "Highlight courses selected by student in a schedule a different color/shade to signify where the students' classes are on UT campus."

**Current Behavior**

`CampusMap.tsx` accepts `processedCourses` (the student's schedule meetings, structured per meeting) but does not use the building location data to change any marker's appearance. All buildings get the same `#BF5700` fill. There is no concept of "enrolled buildings" in the current rendering logic.

**Affected Components**

- `src/views/components/map/Map.tsx` (256 lines): builds `processedCourses` from `useActiveSchedule()`, passes it to `CampusMap`. This is where building codes need to be extracted from meeting locations.
- `src/views/components/map/CampusMap.tsx` (821 lines): renders the SVG campus map. This is where the highlighted color is applied when a building's ID matches an enrolled course's location.
- `src/views/components/map/types.ts`: defines `ProcessInPersonMeetings` with a `location: CourseMeeting['location']` field (an object with `.building` and `.room`).
- `src/views/components/map/graphNodes.ts`: contains all building/intersection/walkway nodes; building node IDs correspond to the `location.building` values in course meetings.
- `src/views/components/map/utils.ts`: existing pure utility functions; the new `extractEnrolledBuildings` function was added here.

No backend changes are needed. The schedule data is already loaded in state via `useActiveSchedule()`.

**Toolkit**

- Tool: GitHub REST API (unauthenticated) + direct clone and source read, used to understand the two components that needed to change before writing a line of code
- What it produced: confirmation that `CampusMap` already receives the schedule data via `processedCourses.location?.building` (used for daily path routing), confirming the data is there and the only missing piece is the fill-color check
- Difficulties: the agent that fetched raw file content via GitHub API was restricted from quoting verbatim (source quoting policy); cloning locally resolved this — I read the full source after cloning
- What changed: I added `utils.ts` to the scope after cloning, since it was the natural place for a pure extraction function that needed to be tested in isolation

---

## Reproduction Process

**Environment Setup**

- OS: Windows 11 Pro for Workstations
- Investigation date: November 24–25, 2026
- Tools used: git clone, pnpm, direct file read
- Local clone: `C:\Users\User\Desktop\UT-Registration-Plus`
- Branch: `feat/highlight-enrolled-buildings` (created from `main`)

The project requires Node.js (^24.14) and pnpm (^10). Node 24.19.0 and pnpm 11.20.0 were already installed. The Chrome extension is built with Vite and loaded unpacked in Chromium for manual testing; that step was not performed (no Chromium automation available in this environment).

**Steps to Reproduce (the missing feature)**

1. Install UT Registration Plus from source (clone, `pnpm install`, `pnpm dev`, load unpacked extension in Chrome)
2. Add several courses to a schedule (courses with in-person meetings at real buildings)
3. Open the extension and navigate to the UTRP Map view
4. Observe: all building markers on the campus map are identical burnt orange; there is no visual distinction for buildings that host enrolled courses

**Reproduction Evidence**

The feature's absence was confirmed by reading the actual source:

- `CampusMap.tsx` lines 621–644 (before this branch): the fill logic is a ternary that checks `selected.start`, `selected.end`, `node.type === 'building'`, and `node.type === 'intersection'`. There is no check against `processedCourses` or any enrollment-based set. All buildings unconditionally get `#BF5700`.
- `Map.tsx` lines 157–158 (before this branch): `CampusMap` is called as `<CampusMap processedCourses={processedCourses} />` with no `highlightedBuildings` prop — confirming no highlighting data is passed.
- The diff (`git diff main...HEAD`) shows the full before/after change: 7 lines removed from the fill logic, 9 added; the `highlightedBuildings` prop and its use in `importantBuildings` did not exist before.

**Toolkit**

- Tool: git clone + source read (`Read` tool on local files), used to read the actual fill logic and confirm the feature is absent
- What it produced: exact before-state code from `CampusMap.tsx` and `Map.tsx` confirming the feature absence; also confirmed `processedCourses.location?.building` is already used in the existing daily-path feature (lines 157-160 in CampusMap), showing the data is already flowing
- Difficulties: could not capture a visual screenshot (no Chrome extension runtime in test environment); code read is the substitute evidence
- What changed: I added the `processedCourses.location?.building` observation (that it is already used for paths) to my understanding, which made the solution cleaner — I only needed to extract building codes and pass them as a prop, not restructure any data flow

---

## Solution Approach

**Analysis**

`processedCourses` is a flat `ProcessInPersonMeetings[]` where each entry has `location?: { building: string; room: string }`. `CampusMap` already receives this array and already uses `location?.building` internally (for the daily walking-path feature). The only missing step is: (1) deriving a set of enrolled building codes from `processedCourses`, and (2) using that set to change the fill color of matching building nodes in the SVG.

**Proposed Solution**

1. **`utils.ts`:** Add `extractEnrolledBuildings(processedCourses)` — a pure function that iterates `processedCourses`, reads `meeting.location?.building`, adds non-nullish values to a `Set<string>`, and returns it as `ReadonlySet<string>`. This keeps the extraction testable in isolation without React hooks or browser APIs.

2. **`Map.tsx`:** After `processedCourses` is derived from `useActiveSchedule()`, call `extractEnrolledBuildings` inside `useMemo`:
   ```typescript
   const highlightedBuildings = useMemo(
       () => extractEnrolledBuildings(processedCourses),
       [processedCourses]
   );
   ```
   Pass to `<CampusMap processedCourses={processedCourses} highlightedBuildings={highlightedBuildings} />`.

3. **`CampusMap.tsx`:** Add `highlightedBuildings?: ReadonlySet<string>` to `CampusMapProps`. In `importantBuildings` useMemo, add enrolled buildings to the result set via `for...of` (so they always render at any zoom/clustering level). In the fill ternary, prepend:
   ```typescript
   node.type === 'building' && (highlightedBuildings?.has(id) ?? false)
       ? '#F8971F'   // UT gold — enrolled course building
       : node.type === 'building'
         ? '#BF5700' // burnt orange — other building
   ```

**Implementation Plan (actual steps taken)**

1. Cloned `Longhorn-Developers/UT-Registration-Plus` to `C:\Users\User\Desktop\UT-Registration-Plus` from upstream (no fork yet — fork was created later)
2. Created branch `feat/highlight-enrolled-buildings`
3. Ran `pnpm install`
4. Read full source of `Map.tsx` and `CampusMap.tsx`
5. Added `ProcessInPersonMeetings` to `utils.ts` import; added `extractEnrolledBuildings` function
6. Added `useMemo` to `Map.tsx` imports; derived `highlightedBuildings`; updated `CampusMap` call
7. Updated `CampusMapProps` and `importantBuildings` useMemo in `CampusMap.tsx`; updated fill logic
8. Created `src/views/components/map/tests/extractEnrolledBuildings.test.ts` with 5 tests
9. Ran `pnpm test --run extractEnrolledBuildings` → 5 passed; `pnpm check-types` → 0 errors; `pnpm lint` → 0 errors
10. Committed as `cbf1be6`
11. User forked repo; pushed branch to `masmahmoodi/UT-Registration-Plus`; PR opened as #1081

**Deviation from initial plan:** Initial design assumed `location` was a plain string. The actual type is `CourseMeeting['location']` (object with `.building` and `.room`). The extraction uses `meeting.location?.building` instead of `meeting.location`. Outcome unchanged; only the field access path differed.

**Toolkit**

- Tool: `git`, `pnpm` (local), used to clone, branch, implement, and verify
- What it produced: 4 files changed, commit `cbf1be6`; focused feature tests passed (5/5), type checking passed, and lint passed with 3 unrelated existing warnings; the full test suite had 14 suites pass and 1 unrelated pre-existing UnoCSS test-environment failure (see test evidence)
- Difficulties: Biome lint flagged the initial `forEach(b => result.add(b))` as returning a non-void value inside a callback — fixed with `for...of`; `ProcessInPersonMeetings.location` was not a plain string as initially assumed — fixed after reading the actual source
- What changed: I added `utils.ts` to the scope (not in the initial plan from API exploration) because a pure function there is the right design; I used `ReadonlySet<string>` instead of `string[]` for the prop type to make immutability explicit

---

## Testing Strategy

The project uses vitest (^4.1.3). I created one test file:
`src/views/components/map/tests/extractEnrolledBuildings.test.ts`

**Tests written and results (November 24, 2026):**

```
pnpm test --run extractEnrolledBuildings

 Test Files  1 passed (1)
      Tests  5 passed (5)
   Start at  21:07:02
   Duration  1.49s (transform 62ms, setup 0ms, import 116ms, tests 4ms, environment 1.05s)
```

Five tests, all passed:
1. `returns an empty set for an empty course list` — PASS
2. `collects the building from a single meeting` — PASS
3. `deduplicates buildings that appear in multiple meetings` — PASS
4. `excludes meetings whose location is null` — PASS
5. `handles multiple distinct buildings` — PASS

**Full suite (for context):**

```
pnpm test --run

 Test Files  14 passed | 1 failed (15)
      Tests  80 passed (80)
   Duration  8.72s
```

The 1 failing suite is `src/views/components/calendar/utils.test.ts`, which fails due to a pre-existing UnoCSS test-environment issue (`file:///__uno.css` cannot be resolved as a file URL). This failure exists on `main` before this branch and is unrelated to the map changes. The 5 tests in my new file are part of the 14 passing suites.

**Type check:** `pnpm check-types` → 0 errors (no output).

**Lint:** `pnpm lint` → 0 errors, 3 pre-existing warnings in files I did not touch.

**Disclosed shortfall:** Chrome extension runtime (`chrome.runtime.getManifest()` in Map.tsx) prevents rendering the full components in vitest without a Chromium harness. Before/after visual screenshots were not captured. The pure-function tests for `extractEnrolledBuildings` cover the code path that determines which buildings get highlighted; TypeScript type checking confirms the prop wiring is correct.

**Toolkit**

- Tool: vitest (^4.1.3), run via `pnpm test --run extractEnrolledBuildings` and `pnpm test --run`
- What it produced: 5 passed tests for the new function; 80 passed tests overall; 1 pre-existing failing suite confirmed unrelated to this change
- Difficulties: Chrome extension runtime prevents unit-testing the full `UTRPMap` / `CampusMap` components in vitest; `extractEnrolledBuildings` was designed as a pure function specifically so it could be tested without any browser context
- What changed: I narrowed the test scope to the pure extraction function rather than attempting a component render test; I verified the type checker as a substitute for the end-to-end wiring test

---

## Implementation Notes

**November 24–25, 2026**

Evaluated and rejected two earlier candidates from the CodePath AI301 Issue Selection site (HHS/simpler-grants-gov #6599, rejected by rubric for blocked prerequisite; LibreSign/libresign #9473, already merged). Ran the issue-select rubric on #864 — SAFE TO CLAIM.

Studied the map module via GitHub REST API structural summaries (pre-clone), then cloned upstream to `C:\Users\User\Desktop\UT-Registration-Plus`. Read full source of:
- `src/views/components/map/Map.tsx` (256 lines)
- `src/views/components/map/CampusMap.tsx` (821 lines)
- `src/views/components/map/types.ts` (161 lines)
- `src/views/components/map/utils.ts` (112 lines)

Implemented feature on branch `feat/highlight-enrolled-buildings`:
- `utils.ts`: added `extractEnrolledBuildings()` with JSDoc
- `Map.tsx`: added `useMemo` import and `extractEnrolledBuildings` import; derived `highlightedBuildings`; updated `CampusMap` call
- `CampusMap.tsx`: added `highlightedBuildings?: ReadonlySet<string>` to props; added enrolled buildings to `importantBuildings` via `for...of`; updated fill ternary to check `highlightedBuildings?.has(id)` before the existing building type check
- New test file `src/views/components/map/tests/extractEnrolledBuildings.test.ts`

Focused feature tests passed (5/5), type checking passed, and lint passed with 3 unrelated existing warnings; the full test suite had 14 suites pass and 1 unrelated pre-existing UnoCSS test-environment failure. Committed as `cbf1be6`.

Created `plan.md`, `plan-comment.md`, `pr_draft.md`, `test_evidence.md`, and `ship-manifest.md` in the fork directory. Manually evaluated plan-check criteria (5 checks, all PASS, verdict: ACCEPT) and pr-precheck criteria (4 checks, all PASS, verdict: ACCEPT) by reading each criterion and assessing it against the plan and diff; no executable plan-check or pr-precheck tool exists on this machine. Results recorded in `ship-manifest.md`. This evaluation was done after implementation; next time I would apply the rubric before writing the first line of implementation code.

User posted claim comment: https://github.com/Longhorn-Developers/UT-Registration-Plus/issues/864#issuecomment-7153678976 (November 25, 2026).

User forked repo, pushed branch, opened PR: https://github.com/Longhorn-Developers/UT-Registration-Plus/pull/1081 (November 25, 2026).

**Toolkit**

- Tool: git clone + pnpm (local), used to run type checking, linting, tests, and create all working files; plan-check and pr-precheck rubric criteria evaluated manually (no executable tool exists)
- What it produced: 4 source files changed; focused feature tests passed (5/5), type checking passed, and lint passed with 3 unrelated existing warnings; the full test suite had 14 suites pass and 1 unrelated pre-existing UnoCSS test-environment failure; manual rubric review recorded ACCEPT for plan-check (5/5) and pr-precheck (4/4) in `ship-manifest.md`
- Difficulties: gh CLI not installed — fork and PR required browser action; check-runs API 404'd for cbf1be6 short SHA so CI results were not available via API; `mergeable_state: "unstable"` on the PR API suggests CI is running or has an issue; no executable plan-check or pr-precheck tool exists so criteria had to be applied manually
- What changed: will install gh CLI before next contribution to avoid all browser-dependent steps; would run rubric criteria before implementation, not after

---

## Pull Request

**PR Link:** https://github.com/Longhorn-Developers/UT-Registration-Plus/pull/1081

**PR Title:** feat: highlight enrolled course buildings on campus map

**PR Description:**

> ### Summary
>
> Buildings where the student has in-person meetings now render in UT gold (`#F8971F`) on
> the campus map, visually distinguishing them from other buildings (burnt orange, `#BF5700`).
>
> ### Changes
>
> - `utils.ts`: add `extractEnrolledBuildings` — a pure function that reads `location?.building`
>   from each `ProcessInPersonMeetings` entry and returns a deduplicated `ReadonlySet<string>`.
> - `Map.tsx`: derive `highlightedBuildings` via `useMemo` and pass as a new optional prop to `CampusMap`.
> - `CampusMap.tsx`: accept `highlightedBuildings?: ReadonlySet<string>`; add enrolled buildings
>   to `importantBuildings` so they always render at any zoom level; apply `#F8971F` fill when
>   a building's ID is in the set.
> - `tests/extractEnrolledBuildings.test.ts`: 5 vitest unit tests (all pass).
>
> ### Testing
>
> - `pnpm test --run extractEnrolledBuildings`: 1 test file, 5 tests, all passed.
> - `pnpm check-types`: 0 errors.
> - `pnpm lint`: 0 errors, 3 pre-existing warnings in unrelated files.
> - Full suite: 14 test files passed, 80 tests passed. 1 pre-existing failing suite
>   (`src/views/components/calendar/utils.test.ts`, UnoCSS test-env issue) exists on `main`.
> - Visual smoke test not performed: Chrome extension runtime prevents rendering map components
>   in vitest; end-to-end screenshot would require loading the extension unpacked in Chromium.
>
> Fixes #864

**Maintainer Feedback:**

- November 25, 2026: none yet — `github-actions[bot]` posted an automated Discord notification comment; 0 human reviews; 0 review comments; PR is open and awaiting maintainer review.

**CI Status:**

- `mergeable_state`: "unstable" (GitHub API, as of November 25, 2026)
- Check-runs: not available via API (endpoint returned 404 for commit `cbf1be6`; no legacy statuses registered)
- Unable to determine which CI jobs exist or their pass/fail status; will update when CI results appear on the PR page

**Status:** Open — awaiting review

**Toolkit**

- Tool: manual pr-precheck rubric review — no executable `pr-precheck` tool exists on this machine; I evaluated each of the 4 criteria by hand against `plan.md`, `git diff main...HEAD`, `pr_draft.md`, and `test_evidence.md`, and recorded each result in `ship-manifest.md` in the fork directory
- What it produced: ACCEPT verdict (4 checks, all PASS) recorded manually in `ship-manifest.md`; not a program's output
- Difficulties: the evaluation was done after the PR was already open; `~/.claude/pr-monitor/monitor.py` does not exist, so automated PR monitoring was not possible; PR state was checked by direct GitHub API calls instead
- What changed: I will apply the rubric criteria before opening the PR on future contributions; and check whether executable tools exist before claiming they "ran"

---

## Learnings and Reflections

**Technical Skills Gained**

- How the UT Registration Plus map module works: `Map.tsx` owns schedule data and derives `processedCourses`; `CampusMap.tsx` owns the SVG rendering; they connect through a typed prop — this two-layer design made the highlight feature a clean addition to both sides
- How `useMemo` and `ReadonlySet` work together to avoid prop-driven re-renders when the schedule hasn't changed
- How Biome's lint rules interact with `Set.prototype.forEach` callbacks (returning `Set.add`'s return value triggers a lint error; `for...of` is the idiomatic alternative)
- How Chrome extension runtime APIs (`chrome.runtime.getManifest()`) affect test strategy — components that call these APIs cannot be tested with vitest alone; pure helper functions are the testable surface
- How to use the GitHub REST API to study a codebase structure before cloning

**Challenges Overcome**

The biggest challenge was the issue selection itself. Two candidates had to be rejected before #864: one because a tool caught a real problem (blocked prerequisite with TBD criteria), one because it was already merged. This took time but prevented working on the wrong issue.

A second challenge was `ProcessInPersonMeetings.location` not being a plain string. The initial plan (from API structural summaries, before reading the actual source) assumed `location` was the building code directly. Reading the source revealed it is an object with `.building` and `.room`. This was discovered immediately after cloning and before any code was written, so the fix was a one-line adjustment.

The lack of gh CLI made every GitHub action (fork, claim comment, PR) require a browser. Installing it before the next contribution would remove all those friction points.

**What I'd Do Differently Next Time**

I would install gh CLI before starting so I can post claim comments, fork repos, and create PRs without browser actions. I would also run plan-check before writing the first line of implementation code — not after. I would set up the Chrome extension locally to get a visual before/after screenshot, since the type checker and unit tests do not substitute for a live rendering check.

---

## Resources Used

- https://github.com/Longhorn-Developers/UT-Registration-Plus/issues/864 — the issue itself
- https://github.com/Longhorn-Developers/UT-Registration-Plus/pull/1081 — the PR I opened
- https://github.com/Longhorn-Developers/UT-Registration-Plus/issues/864#issuecomment-7153678976 — my claim comment
- https://github.com/Longhorn-Developers/UT-Registration-Plus/pull/868 — the previous abandoned implementation; source of approach validation
- https://raw.githubusercontent.com/Longhorn-Developers/UT-Registration-Plus/main/src/views/components/map/Map.tsx — read to understand data flow (pre-clone)
- https://raw.githubusercontent.com/Longhorn-Developers/UT-Registration-Plus/main/src/views/components/map/CampusMap.tsx — read to understand current building rendering (pre-clone)
- https://raw.githubusercontent.com/Longhorn-Developers/UT-Registration-Plus/main/src/views/components/map/types.ts — read to understand `ProcessInPersonMeetings.location` type (pre-clone)
- https://api.github.com/repos/Longhorn-Developers/UT-Registration-Plus/contents/src/views/components/map — directory listing for the map module
- https://api.github.com/repos/Longhorn-Developers/UT-Registration-Plus/contents/src/views/hooks — directory listing for hooks including `useSchedules.ts`
