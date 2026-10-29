# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

**Size rule:** keep this file and `docs/framework/conventions.md` each under 200 lines and 30 KB, no line over 500 characters. A rule that holds for any project built on the framework is in `conventions.md`, a core file kept as the template ships it; a rule about this project (its app, its other folders, its CI and branches) goes here.

The framework's conventions, which the sections below add to:
@docs/framework/conventions.md

## Project

<!-- Replace this section with what your project tests. -->
Created from [SimpRightTemplate](https://github.com/Zaytsman/SimpRightTemplate) (`template` in `simpright.yml`): a TypeScript + Playwright framework for UI and API tests, with YAML scenarios checked by a validator. Its examples test the Toolshop demo app, logged out; replace them with your own.

## Commands

```bash
npm install
npm run install:browsers          # Chromium + OS deps
npm test                          # both projects (api, ui)
npm run test:api                  # API project only
npm run test:ui                   # UI project only
npm run test:smoke                # the tests tagged @smoke
npx playwright test -g "UI-001:"  # one test by scenario ID
npm run typecheck                 # tsc --noEmit (unused locals and parameters fail it)
npm run validate:scenarios        # scenario and bug files, IDs and specs; prints the next free IDs
npm run check:step-titles         # step titles vs scenario steps, from a JSON report (default test-results/results.json)
npm run doctor                    # simpright.yml and its paths, the core's imports, the core vs SimpRightTemplate (reads GitHub)
npm run inspect:ui -- / --logged-out   # page inspector: data-test elements + accessibility tree
npm run report                    # open the last HTML report
```

`validate:scenarios`, `check:step-titles` and `doctor` come from the [SimpRight Kit](https://github.com/Zaytsman/SimpRightKit)'s npm packages (`@simpright/scenarios`, `simpright`; devDependencies pinned to one version and bumped together), and so do the scenario and bug schemas (`paths.scenarioSchema`, `paths.bugSchema`).

## The app under test

<!-- Replace this section with your app's facts: its login, where it keeps the token, what every page has, its quirks. -->
- The examples test the Toolshop ([practicesoftwaretesting.com](https://practicesoftwaretesting.com), API at api.practicesoftwaretesting.com). The test-id attribute is `data-test`.
- No login is set up (`src/config/project.ts`): every example runs logged out (`authMode: 'none'`, scenario role `guest`).
- The demo site re-seeds every hour at :00 UTC; for a minute or two, calls can fail.

## CI

- `pr-checks.yml` (`verify`): typecheck, scenario validation and `doctor --offline` on pull requests and pushes to `main` (offline: the template is its own reference; a project made from it can drop `--offline`). `tests.yml`: both projects, headless, on the same events and by hand; the HTML report is kept as an artifact.
- Every action is pinned to a full commit SHA with its version in a comment; every checkout sets `persist-credentials: false`; `npm ci` runs with `--ignore-scripts`. Secrets reach a step by name in its `env:`, never through `secrets: inherit`.
