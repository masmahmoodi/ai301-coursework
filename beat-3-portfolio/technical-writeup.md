# Highlighting Enrolled Course Buildings on the UTRP Map

## Overview

For my open-source contribution, I worked on UT Registration Plus, a browser extension that helps University of Texas students work with course registration information. Issue #864 asked for courses from a student's active schedule to be highlighted on the UTRP campus map so that the buildings connected to those courses would be easier to identify. I implemented the feature by extracting building codes from scheduled in-person meetings, passing those buildings into the map component, highlighting them in UT gold, and adding focused unit tests. I opened pull request #1081 against the original project, and the PR is currently open and awaiting maintainer review: https://github.com/Longhorn-Developers/UT-Registration-Plus/pull/1081

## What happened

I started by reading the existing map and schedule code instead of immediately changing the UI. I looked through `Map.tsx`, `CampusMap.tsx`, the map types, and the utilities used by the feature. I found that the processed course data already included a location object with a building code, and `CampusMap` was already using building IDs for other map behavior. That meant I did not need to create a new data source. I only needed a clean way to identify which existing map buildings belonged to courses in the active schedule.

I added a small utility called `extractEnrolledBuildings` that reads the building value from each in-person meeting and stores the unique building codes in a set.

```ts
export const extractEnrolledBuildings = (
    processedCourses: ProcessInPersonMeetings[],
): ReadonlySet<string> => {
    const buildings = new Set<string>();

    for (const meeting of processedCourses) {
        const building = meeting.location?.building;
        if (building) buildings.add(building);
    }

    return buildings;
};
```

This is one piece of proof from my implementation because it shows how the feature derives the highlighted buildings directly from the student's processed schedule. Using a `Set` also prevents the same building from being added multiple times when more than one course meets there.

In `Map.tsx`, I used `useMemo` to calculate the highlighted building set from `processedCourses` and passed it into `CampusMap`. I added `highlightedBuildings` as an optional `ReadonlySet<string>` prop so the new behavior stayed contained within the map feature instead of changing unrelated parts of the application.

Inside `CampusMap`, I added the enrolled buildings to the set of important buildings. This makes them remain visible even when the map changes which normal buildings are displayed because of zoom level or clustering. I also changed the building color logic so a building in the active schedule uses UT gold while other buildings keep the existing color.

```ts
node.type === 'building' &&
(highlightedBuildings?.has(id) ?? false)
    ? '#F8971F'
    : node.type === 'building'
      ? '#BF5700'
```

This second piece of proof shows the visible result of the feature. Buildings connected to the student's active schedule receive `#F8971F`, while other building nodes continue using the existing `#BF5700` color. I intentionally kept the change small instead of redesigning the map or changing behavior unrelated to issue #864.

One useful moment happened when I ran the linter on my first implementation. My initial code used a `forEach` approach that Biome did not accept cleanly in the way I had written it. Instead of ignoring the lint result, I changed the implementation to a straightforward `for...of` loop. I ran the checks again after the change, and the lint command completed with zero errors. It still reported three warnings, but those warnings were in existing files that I had not modified. This was a good example of changing my own implementation after a tool exposed a problem rather than treating the first version as finished.

I also added five focused Vitest tests for `extractEnrolledBuildings`. The cases covered an empty course list, one building, duplicate buildings, a meeting with a missing location, and multiple different buildings. The focused tests all passed.

My final local verification was:

```text
Focused feature tests: 5/5 passed
Type check: passed with 0 type errors
Lint: passed with 0 errors and 3 unrelated existing warnings
Full test suite: 14 test suites passed and 1 unrelated existing suite failed
80 tests passed overall
```

The remaining full-suite failure came from `src/views/components/calendar/utils.test.ts` and involved the existing UnoCSS `file:///__uno.css` test-environment behavior. Because that failure was outside the files I changed, I documented it instead of claiming that the complete test suite was green.

After testing the implementation, I pushed the branch `feat/highlight-enrolled-buildings` to my fork and opened pull request #1081 against `Longhorn-Developers/UT-Registration-Plus:main`. The PR contains four changed files: `CampusMap.tsx`, `Map.tsx`, `utils.ts`, and the new `extractEnrolledBuildings.test.ts` file. The PR also references issue #864 so the proposed change is connected to the original request.

A concrete result happened after the PR entered the upstream project's workflow: the repository's GitHub automation created a Longhorn Developers Discord thread for the PR and posted a bot comment about it. That showed that my contribution had reached the project's normal review process rather than remaining only in my local repository. The PR is still open and has not received a human review yet, so I describe the outcome as an opened contribution rather than a completed or merged fix.

## Limitations and what's next

The biggest limitation is that pull request #1081 is still awaiting maintainer review, so the implementation may still need changes before the project accepts it. I would respond to any review comments and update the branch if the maintainers prefer a different color, data flow, or map behavior.

Another limitation is that the feature depends on the building codes in the processed schedule matching building IDs that exist in the map data. The focused unit tests verify the extraction logic, but they do not fully simulate a student's real schedule being rendered through the complete map interface. A useful next step would be to add a higher-level component or integration test that provides a realistic active schedule and confirms that the correct campus building is visibly highlighted.

The repository also currently has the unrelated UnoCSS test-environment failure in the broader test suite. My feature tests, type checking, and lint checks passed, but I would not describe the repository as having a completely green test suite until that separate problem is resolved.

The most important thing I learned from this contribution was that working in an existing codebase is less about writing a large amount of new code and more about finding the smallest place where the new behavior belongs. Reading the existing data flow first helped me avoid duplicating schedule information or redesigning the map. If I continued the contribution, my next steps would be to respond to maintainer feedback, strengthen the map-level testing, and make any changes needed to get the PR ready for merge.
