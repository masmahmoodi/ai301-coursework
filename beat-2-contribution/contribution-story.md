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
**Pull Request:** pending — see Implementation Notes for current status

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

I used the CodePath AI301 Issue Selection site to search for issues matching my background in TypeScript, JavaScript, and backend development. I filtered for `good first issue`, `bug`, and `improvement` labels, then inspected several candidates. The main problem I found was that issues could appear open and available even when they were already closed, merged, or blocked by an unresolved prerequisite. The issue-select rubric caught two of these problems explicitly: one candidate (HHS/simpler-grants-gov #6599) returned REJECT because it was blocked by an open prerequisite issue with acceptance criteria marked "TBD" and had been deprioritized by the maintainer; a second candidate (LibreSign/libresign #9473) was already fully closed — PR #9483 had been merged on November 15, 2026, two weeks before I evaluated it. After these rejections I evaluated UT-Registration-Plus #864 ("Highlight Courses on UTRP Map") and confirmed it was SAFE TO CLAIM: open, unassigned, no competing open PRs, no human claim comments, and a previous draft PR (#868) that had been abandoned and closed on September 10, 2026 without merge.

**Unit 6: Drafted claim comment, investigated codebase, implemented feature, and ran tests**

I drafted a claim comment for issue #864 following the repro-check voice guide rules (see `claim-draft.md` — still needs manual browser post). I then cloned the repository to `C:\Users\User\Desktop\UT-Registration-Plus`, read the full source of the two central files (`Map.tsx` at 256 lines, `CampusMap.tsx` at 821 lines), and implemented the feature on branch `feat/highlight-enrolled-buildings` (commit `cbf1be6`). The key discovery from reading the actual source: `ProcessInPersonMeetings.location` is typed as `CourseMeeting['location']` — an object with `.building` and `.room` fields, not a plain string — and `CampusMap` already uses `location?.building` internally for the daily path feature. I added `extractEnrolledBuildings` as a pure function in `utils.ts`, called it in `Map.tsx` via `useMemo`, and passed the result to `CampusMap` as a new `highlightedBuildings?: ReadonlySet<string>` prop. In `CampusMap`, enrolled buildings are added to `importantBuildings` (so they always render regardless of zoom or clustering) and their SVG circles get fill `#F8971F` (UT gold) instead of `#BF5700` (burnt orange). I wrote 5 vitest unit tests for `extractEnrolledBuildings`; all 5 passed. TypeScript type check (`pnpm check-types`) passed with zero errors. Biome linter (`pnpm lint`) passed with zero errors (3 pre-existing warnings in files I did not touch).

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I found issue #864 on the CodePath AI301 Issue Selection site while searching for TypeScript/JavaScript issues. The issue is titled "Highlight Courses on UTRP Map" and asks for a visual feature on the UT Registration Plus Chrome extension's campus map: courses a student has selected in their schedule should be highlighted in a different color or shade on the map, so they can quickly see which buildings they need to visit. The scope is well-defined — the map already exists, the schedule data is already in state, and the task is connecting the two — which fits the beginner contribution profile I was looking for.

I confirmed the issue was available by running the issue-select rubric. All five required checks passed: the repo is active (last commit within days), no one is assigned, no competing open or draft PRs exist, no human has posted a claim comment (the only comment is from `linear[bot]`, a project management automation), and the repo has no CONTRIBUTING.md, which means no stated restriction on AI-assisted contributions. A previous attempt (PR #868, by arunbagavathiannan) was closed without merge on September 10, 2026, and was in a dirty merge state at close — it is not competition.

Claim comment: pending — needs to be posted manually at https://github.com/Longhorn-Developers/UT-Registration-Plus/issues/864 (see claim-draft.md). I will add the permanent link here the moment it is posted.

**Toolkit**

- Tool: issue-scout (the rubric-driven grading tool at `~/.claude/skills/issue-scout/`), used to evaluate UT-Registration-Plus #864 against the five required checks in `scope.md` and `rubric.md`
- What it produced: a SAFE TO CLAIM verdict with evidence; full output saved to `scan-output.md` and `scan-notes.md`
- Difficulties: two earlier candidates had to be rejected before this one; the rubric correctly caught a blocked issue (HHS #6599, newcomer-scope fail) and an already-merged issue (LibreSign #9473, state: closed); those rejections cost time but prevented working on a dead end
- What changed: I updated `scope.md` to reflect the CodePath AI301 Issue Selection site as the new source; after the HHS and LibreSign rejections, I added a step to check closed state and PR merge status before treating any issue as a candidate

---

## Understanding the Issue

**Problem Description**

The UT Registration Plus Chrome extension includes a campus map that shows UT Austin buildings with an SVG overlay. Students can view the map alongside their selected schedule. Currently, all buildings render identically — every building marker uses the same burnt orange color (`#BF5700`) regardless of whether the student has a class there. A student looking at the map cannot immediately tell which buildings their enrolled courses meet in.

**Expected Behavior**

Buildings that contain rooms for courses in the student's active schedule should be visually distinguished from other buildings — a different color or shade should mark them. The issue body says: "Highlight courses selected by student in a schedule a different color/shade to signify where the students' classes are on UT campus."

**Current Behavior**

`CampusMap.tsx` accepts `processedCourses` (the student's schedule meetings, keyed by day) but does not use the building location data to change any marker's appearance. All buildings get the same `#BF5700` fill. There is no concept of "enrolled buildings" in the current rendering logic.

**Affected Components**

Based on codebase investigation on November 24, 2026:

- `src/views/components/map/Map.tsx`: builds `processedCourses` from `useActiveSchedule()` and passes it to `CampusMap`. This is where building codes need to be extracted from meeting locations.
- `src/views/components/map/CampusMap.tsx`: renders the SVG map. This is where the highlighted color must be applied when a building's ID matches an enrolled course's location.
- `src/views/components/map/types.ts`: defines `ProcessInPersonMeetings` with a `location` field (the building code, e.g. "ETC", "JES") and `MapNode` for graph nodes.
- `src/views/components/map/graphNodes.ts`: contains the building, intersection, and walkway nodes; building node IDs correspond to the `location` values in course meetings.

No backend changes are needed. The schedule data is already loaded in state via `useActiveSchedule()`.

**Toolkit**

- Tool: GitHub REST API (unauthenticated), used to fetch directory listings and raw file content for the map module
- What it produced: a clear picture of the two files to change (`Map.tsx` and `CampusMap.tsx`), the type structure (`ProcessInPersonMeetings.location`), and confirmation that no highlighting logic exists in the current code
- Difficulties: the agent that fetched the raw files was restricted from quoting file contents verbatim (source not pre-approved in the tool's config); I had to work from structural summaries; full source will be read after cloning the fork locally
- What changed: I added fetching of `types.ts` and `useFlattenedCourseSchedule.ts` to understand the data shape before writing the solution; this avoided guessing about what `processedCourses` contains

---

## Reproduction Process

**Environment Setup**

- OS: Windows 11 Pro for Workstations
- Investigation date: November 24, 2026
- Tools used: GitHub REST API (unauthenticated), raw.githubusercontent.com
- Note: gh CLI is not installed on this machine; a full clone and local run is pending fork creation (browser action required)

The project requires Node.js (^24.14) and pnpm (^10) for local development. The Chrome extension is built with Vite and loaded unpacked in a Chromium browser for manual testing.

**Steps to Reproduce (the missing feature)**

1. Install UT Registration Plus from source (clone, `pnpm install`, `pnpm dev`, load unpacked extension in Chrome)
2. Add several courses to a schedule (courses with in-person meetings at real buildings, e.g. ETC 2.136)
3. Open the extension and navigate to the UTRP Map view
4. Observe: all building markers on the campus map are identical burnt orange; there is no visual distinction for buildings that host your enrolled courses
5. Expected: buildings where enrolled courses meet should appear in a different color or shade

**Reproduction Evidence**

- **Codebase confirmation (November 24, 2026):** Fetched `CampusMap.tsx` (33,820 bytes) via raw.githubusercontent.com. The component accepts `processedCourses` but the building rendering loop applies `#BF5700` uniformly to all building nodes with no conditional based on enrollment status. No `highlightedBuildings` or similar prop exists in the current `main` code.
- **Type confirmation:** `types.ts` defines `ProcessInPersonMeetings` with a `location` field. The `location` values are building codes that correspond to `MapNode` IDs in `graphNodes.ts`. The data to drive highlighting is already present in `processedCourses`.
- **Previous attempt (PR #868, closed Sep 2026):** Confirms the feature was attempted before. The PR added a `highlightedBuildings` prop and yellow fill to `CampusMap.tsx`. It was closed without merge (dirty merge state, debug `console.log` left in, closed as draft). This confirms the approach is feasible; it also confirms no solution is currently on `main`.

**Toolkit**

- Tool: GitHub REST API and raw file fetches (via agent), used to fetch file sizes, directory listings, and structural summaries of the map components
- What it produced: confirmation that the missing feature is real, that the data to implement it already exists in `processedCourses.location`, and that no highlight logic is in `main`
- Difficulties: could not read full file verbatim from agent (source quoting restriction); will read full source after local clone
- What changed: documented the reproduction as a "feature absence" verification rather than a bug crash — the issue is a missing feature, so reproduction means confirming the feature is absent, which the code confirms

---

## Solution Approach

**Analysis**

The feature requires two targeted changes:

1. **Extract enrolled building codes** in `Map.tsx`: after building `processedCourses` from the active schedule, flatten the array, map each entry to its `location` field, filter out non-physical locations (`TBA`, `ONL`, `INTERNET`, empty string), and deduplicate. The result is a `string[]` of building IDs corresponding to rooms in the student's schedule.

2. **Apply highlight color** in `CampusMap.tsx`: add an optional `highlightedBuildings?: string[]` prop. When rendering each building node in the SVG, check whether the node's ID is in `highlightedBuildings`. If yes, use UT gold (`#F8971F`) as the fill; if no, use the existing burnt orange (`#BF5700`).

This keeps the change minimal and localized. No new components, no new state management, no changes to `graphNodes.ts` or `types.ts`.

**Proposed Solution**

`Map.tsx` additions (inside the component, after `processedCourses` is built):

```typescript
const EXCLUDED_LOCATIONS = new Set(['TBA', 'ONL', 'INTERNET', '']);

const highlightedBuildings = useMemo(
    () => [
        ...new Set(
            processedCourses
                .flat()
                .map(c => c.location)
                .filter((loc): loc is string => !!loc && !EXCLUDED_LOCATIONS.has(loc))
        ),
    ],
    [processedCourses]
);
```

Pass to `CampusMap`:

```tsx
<CampusMap processedCourses={processedCourses} highlightedBuildings={highlightedBuildings} />
```

`CampusMap.tsx` changes — update the prop type to accept `highlightedBuildings?: string[]`, then in the building-node rendering logic:

```typescript
const isEnrolled = highlightedBuildings?.includes(node.id) ?? false;
const fill = isEnrolled ? '#F8971F' : '#BF5700';
```

**Implementation Plan**

**What was implemented (November 24, 2026):**

1. Cloned upstream to `C:\Users\User\Desktop\UT-Registration-Plus`
2. Created branch `feat/highlight-enrolled-buildings`
3. Ran `pnpm install`
4. Read full source of `Map.tsx` (256 lines) and `CampusMap.tsx` (821 lines)
5. Added `extractEnrolledBuildings` to `utils.ts`: pure function, reads `location?.building` from each `ProcessInPersonMeetings`, returns `ReadonlySet<string>`
6. Added `useMemo` to `Map.tsx` imports; derived `highlightedBuildings` from `processedCourses`; passed as new prop to `CampusMap`
7. Added `highlightedBuildings?: ReadonlySet<string>` to `CampusMapProps`; added enrolled buildings to `importantBuildings` (so they always render at any zoom); applied `#F8971F` fill for buildings in the set
8. Created `src/views/components/map/tests/extractEnrolledBuildings.test.ts` with 5 vitest unit tests
9. All 5 tests passed: `pnpm test -- extractEnrolledBuildings` → 5 passed (5)
10. `pnpm check-types` → no errors
11. `pnpm lint` → 0 errors, 3 pre-existing warnings (not in my files)
12. Committed: `cbf1be6 feat: highlight enrolled course buildings on campus map (#864)`
13. Pending: fork repo (browser action), push branch, open PR

**Correction from initial plan:** `ProcessInPersonMeetings.location` is `CourseMeeting['location']` — an object with `.building` and `.room` — not a plain string. The extraction uses `meeting.location?.building`, not `meeting.location`. This was discovered by reading the actual source post-clone.

**Toolkit**

- Tool: git clone + pnpm (local), used to read and implement changes in the actual codebase
- What it produced: 4 files changed (utils.ts, Map.tsx, CampusMap.tsx, new test file), commit `cbf1be6`, all checks passing
- Difficulties: initial plan assumed `location` was a string; reading the actual source revealed it is `{ building: string; room: string }` — required adjusting the extraction to `location?.building`; also, the first draft of the `for...of` equivalent used a concise arrow function that Biome's lint flagged as returning a non-void value inside a callback — fixed by switching to `for...of`
- What changed: added `for...of` loop in `importantBuildings` instead of `.forEach(item => set.add(item))` to satisfy the Biome linter; chose `ReadonlySet<string>` instead of `string[]` for the prop type to make immutability explicit

---

## Testing Strategy

The project uses vitest (^4.1.3). Tests live alongside their source (e.g., `src/views/hooks/tests/`, `src/shared/util/tests/`). I created `src/views/components/map/tests/extractEnrolledBuildings.test.ts`.

**Tests written and run (November 24, 2026):**

Test file: `src/views/components/map/tests/extractEnrolledBuildings.test.ts`

```
pnpm test --run extractEnrolledBuildings

 Test Files  1 passed (1)
      Tests  5 passed (5)
   Start at  21:07:02
   Duration  1.49s
```

Five tests, all passed:

1. `returns an empty set for an empty course list` — PASS
2. `collects the building from a single meeting` — PASS
3. `deduplicates buildings that appear in multiple meetings` — PASS
4. `excludes meetings whose location is null` — PASS
5. `handles multiple distinct buildings` — PASS

The test for "exclusion filter" (TBA/ONL/INTERNET) is handled by the null/undefined case — the actual `location` object for TBA courses is `null` (not a string "TBA"), so the `location?.building` check correctly returns `undefined`, which the `if (building)` guard filters out. No separate exclusion list is needed; the guard on optionality is sufficient.

Before/after visual evidence would require loading the extension in Chrome and comparing screenshots. The code path is correct (confirmed by reading source and type checking), but a live screenshot was not captured because the Chrome extension runtime (`chrome.runtime.getManifest()`) requires an actual browser context.

**Toolkit**

- Tool: vitest (^4.1.3), run via `pnpm test --run extractEnrolledBuildings`
- What it produced: 5 passed tests, 0 failures
- Difficulties: `CampusMap.tsx` uses `chrome.runtime` and cannot be unit-tested without a Chromium harness; the pure function `extractEnrolledBuildings` was designed specifically to be testable without any browser context
- What changed: I narrowed the test scope to the pure extraction function rather than attempting a component-level render test, which would require mocking the entire Chrome extension environment

---

## Implementation Notes

**November 24, 2026**

Evaluated and rejected two earlier candidates from the CodePath AI301 Issue Selection site (HHS/simpler-grants-gov #6599, rejected by rubric for blocked prerequisite; LibreSign/libresign #9473, already merged). Ran the issue-select rubric on #864 — SAFE TO CLAIM.

Studied the map module via GitHub REST API structural summaries to understand the approach, then cloned the upstream repo locally to `C:\Users\User\Desktop\UT-Registration-Plus`. Read full source of:
- `src/views/components/map/Map.tsx` (256 lines): uses `useActiveSchedule()`, builds flat `ProcessInPersonMeetings[]`, passes to `CampusMap`
- `src/views/components/map/CampusMap.tsx` (821 lines): renders UT campus SVG; `CampusMapProps` takes `processedCourses: ProcessInPersonMeetings[]`; building circles get `#BF5700` fill with no enrollment check
- `src/views/components/map/types.ts`: `ProcessInPersonMeetings.location` = `CourseMeeting['location']` (object with `.building`, `.room`)
- `src/views/components/map/utils.ts`: pure utility functions; ideal place to add `extractEnrolledBuildings`

Implemented feature on branch `feat/highlight-enrolled-buildings`:
- Added `extractEnrolledBuildings(processedCourses)` to `utils.ts`
- Added `useMemo` import to `Map.tsx`; derived `highlightedBuildings`; passed to `CampusMap`
- Updated `CampusMapProps` and `importantBuildings` useMemo in `CampusMap.tsx`; applied `#F8971F` fill for enrolled buildings

All checks passed:
- `pnpm test --run extractEnrolledBuildings` → 5 passed (5)
- `pnpm check-types` → 0 errors
- `pnpm lint` → 0 errors, 3 pre-existing warnings

Committed as `cbf1be6`. Drafted claim comment (see `claim-draft.md`).

Pending: post claim comment in browser; fork repo; push branch; open PR.

**Toolkit**

- Tool: git clone + pnpm (local development), used to run type checking, linting, and tests against the actual codebase
- What it produced: commit `cbf1be6` on branch `feat/highlight-enrolled-buildings`, 4 files changed, all checks green
- Difficulties: initial design assumed `location` was a plain string; it is `CourseMeeting['location']` (object); corrected after reading source; Biome linter flagged concise arrow function returning `Set.add` return value — fixed with `for...of`
- What changed: gh CLI not installed — fork and PR creation require browser action; will install before next session

---

## Pull Request

**PR Link:** pending — implementation is complete and committed on branch `feat/highlight-enrolled-buildings` (commit `cbf1be6`); PR will be opened after forking the repo (browser action required, gh CLI not installed)

**PR Description:** (ready to post — will be used when opening the PR)

> feat: highlight enrolled course buildings on campus map (#864)
>
> Buildings where the student has in-person meetings now render in UT gold
> (`#F8971F`) on the campus map, distinguishing them from other buildings
> (burnt orange, `#BF5700`).
>
> **Changes:**
> - `utils.ts`: add `extractEnrolledBuildings` — a pure function that reads
>   `location?.building` from each `ProcessInPersonMeetings` entry and returns
>   a deduplicated `ReadonlySet<string>`.
> - `Map.tsx`: derive `highlightedBuildings` via `useMemo` and pass as a new
>   optional prop to `CampusMap`.
> - `CampusMap.tsx`: accept `highlightedBuildings?: ReadonlySet<string>`; add
>   enrolled buildings to `importantBuildings` so they always render at any zoom
>   level; apply `#F8971F` fill when a building's ID is in the set.
> - `tests/extractEnrolledBuildings.test.ts`: 5 vitest unit tests (all pass).
>
> Fixes #864

**Maintainer Feedback:**

- November 24, 2026: none yet — claim comment not yet posted (requires manual browser action; see claim-draft.md); no PR exists yet

**Status:** Awaiting fork creation and implementation

**Toolkit**

- Tool: pr-precheck (the rubric in `tools/pr-precheck/`), reviewed before this section was written
- What it produced: confirmed that a PR requires a matching plan, a branch diff, test evidence, and the repo's PR template — none of which exist yet because the implementation is pending fork creation
- Difficulties: pr-precheck correctly gates PR submission; this is working as designed
- What changed: will re-run pr-precheck once the implementation is complete and tests pass

---

## Learnings and Reflections

**Technical Skills Gained**

- How to study a Chrome extension's TypeScript/Vite codebase structure via GitHub API without a local clone (and what the limits of that approach are)
- How the UT-Registration-Plus map module works: the two-layer design where `Map.tsx` owns the schedule data and `CampusMap.tsx` owns the SVG rendering, connected by a typed `processedCourses` prop
- How to read the graph-node model (`graphNodes.ts`, `MapNode`, `NodeType`) and understand how building IDs in course meetings correspond to SVG nodes on the campus map
- How to evaluate whether a previous abandoned PR (PR #868) is worth adapting vs. starting fresh on `main`

**Challenges Overcome**

The biggest logistical challenge was the issue selection itself. Two candidates had to be rejected before #864: one because a tool caught a real problem (blocked prerequisite with TBD criteria), one because it was already closed and merged. The rubric saved time by making these problems visible before I invested implementation effort.

A second challenge was the tool restriction on quoting raw file content. The agent that fetched the map source files could not reproduce them verbatim due to source quoting limits, so I had to work from structural summaries. This was enough to design the plan, but it means I need to read the actual source post-clone before writing the implementation.

**What I'd Do Differently Next Time**

I would install gh CLI at the start of the assignment so I can post claim comments, fork repos, and work with GitHub programmatically without browser actions. I would also evaluate the dependency chain of issues earlier — checking whether required predecessor issues exist in `main` is now a first-pass filter, not an afterthought.

---

## Resources Used

- https://github.com/Longhorn-Developers/UT-Registration-Plus/issues/864 — the issue itself
- https://github.com/Longhorn-Developers/UT-Registration-Plus/pull/868 — the previous abandoned implementation; source of approach validation
- https://raw.githubusercontent.com/Longhorn-Developers/UT-Registration-Plus/main/src/views/components/map/Map.tsx — read to understand data flow
- https://raw.githubusercontent.com/Longhorn-Developers/UT-Registration-Plus/main/src/views/components/map/CampusMap.tsx — read to understand current building rendering and confirm no highlighting exists
- https://raw.githubusercontent.com/Longhorn-Developers/UT-Registration-Plus/main/src/views/components/map/types.ts — read to understand `ProcessInPersonMeetings.location` and `MapNode` types
- https://api.github.com/repos/Longhorn-Developers/UT-Registration-Plus/contents/src/views/components/map — directory listing for the map module
- https://api.github.com/repos/Longhorn-Developers/UT-Registration-Plus/contents/src/views/hooks — directory listing for hooks including `useSchedules.ts`
