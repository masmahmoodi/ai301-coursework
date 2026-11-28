I recently opened an open-source pull request for UT Registration Plus that highlights campus buildings connected to a student's active course schedule. I built the change so students can identify the locations of their scheduled classes more easily on the campus map.

I traced the project's existing schedule and map code, connected course building information to the map, and added focused tests for the new behavior. I also ran type checking and linting and documented an unrelated existing test-environment failure instead of presenting the entire test suite as passing.

One part of the process I valued was having to revise my first implementation after linting exposed an issue. It reinforced that contributing to an existing project is not only about getting code to work, but also about understanding the codebase, testing carefully, keeping the change focused, and being accurate about the results.

The pull request is currently open and awaiting maintainer review.

https://github.com/Longhorn-Developers/UT-Registration-Plus/pull/1081
