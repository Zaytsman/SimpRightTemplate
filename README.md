# SimpRightTemplate

A starting point for automated UI and API tests with **TypeScript + Playwright**: the framework behind [SimpRight](https://github.com/Zaytsman/SimpRight), without SimpRight's own tests. Every test starts as a scenario in YAML, the spec follows it step by step, and a validator checks that the two match.

The examples test the [Toolshop demo app](https://practicesoftwaretesting.com) logged out, so they pass as soon as you create your repo, with no secrets.

## Quick start

1. Click **Use this template** → **Create a new repository**, then clone your new repository.
2. Install and run (Node.js 22.18 or newer):

   ```bash
   npm ci
   npm run install:browsers
   npm test
   ```

`npm test` runs both Playwright projects: `api` (no browser) and `ui` (Chromium; a visible browser locally, headless on CI).

## What you get

- **The core**, 22 files listed under `core` in `simpright.yml`: the config and environments, `BaseClient` and `TokenService`, `BasePage`, `BaseComponent` and `BaseDialog`, the base fixtures (role, login, cleanup), assertion messages with masking, the HTTP checks and send helpers, `globalSetup`, the page inspector, `playwright.config.ts` and the framework's conventions (`docs/framework/conventions.md`). A core file imports only other core files, the two seams and packages, and the validator checks it.
- **Two seams for your app:** `src/config/project.ts` (the test-id attribute, the login, what runs before and after a run) and `src/config/roles.ts`.
- **Examples:** an API spec (`GET /products/search`, 5 tests) and a UI spec (product search, 5 tests), each with its scenario file, plus the client, service, page object, component and precondition fixture they use.
- **Scenarios and bugs:** YAML files with JSON schemas; `npm run validate:scenarios` checks the scenario and bug files, the IDs, and that every spec matches its scenarios; `npm run check:step-titles` checks the step titles of a run.
- **The page inspector:** `npm run inspect:ui -- <path>` prints a page's `data-test` elements and accessibility tree, read-only, for writing page objects and scenarios.
- **CI:** `verify` (typecheck and the validator) and `Tests` (both projects, the report kept as an artifact) on pull requests and pushes to `main`; Dependabot; every action pinned to a commit SHA.

## A scenario and its test

```yaml
# test-scenarios/ui/products/product-search.yml
- id: UI-001
  name: Search by name shows only matching products
  tags: ["@smoke"]
  role: guest
  preconditions:
    - The home page is open.
  steps:
    - Search for "pliers".
    - 'Verify the caption reads "Searched for: pliers".'
```

```ts
// tests/ui/products/product-search.spec.ts
import { openHomePageTest as test, expect } from '@fixtures';

test('UI-001: Search by name shows only matching products', { tag: '@smoke' }, async ({ homePage }) => {
  await test.step('Search for "pliers".', async () => {
    await homePage.search('pliers');
  });

  await test.step('Verify the caption reads "Searched for: pliers".', async () => {
    await expect(homePage.searchCaption, 'The caption names the search term').toHaveText('Searched for: pliers');
  });
});
```

## Point it at your app

1. **URLs and timeouts:** `src/envs/TEST.json`; more environments as `src/envs/<NAME>.json`, picked with `TEST_ENV`. Secrets go in `.env` (copy `.env.example`) and reach the env file as `${VAR}` placeholders.
2. **Test ids and login:** `src/config/project.ts`. Set your app's test-id attribute, and `login` and `browserState` once tests log in. [SimpRight's project file](https://github.com/Zaytsman/SimpRight/blob/main/src/config/project.ts) is a full example: an API login, a UI login and a user created for each run.
3. **Roles and users:** `src/config/roles.ts`, `roles` in `simpright.yml`, and `testUsers` in the env file.
4. **Your app's code:** what every page has goes in `src/ui/pages/AppPage.ts`; pages, components, clients, services and DTOs go next to the examples, registered as fixtures.
5. **Your tests:** replace the examples with your own scenarios and specs, and list your areas in `simpright.yml` (`ids.areas`).
6. **`CLAUDE.md`:** replace its project sections with your app's facts.

## Layout

```text
src/
  config/          env.ts, defineProject.ts (core); project.ts, roles.ts (the seams)
  envs/            TEST.json: URLs, timeouts, users
  api/             clients, services, DTOs, fixtures, auth, the coverage hook
  ui/              pages (with components and dialogs), fixtures (with preconditions), auth
  fixtures/        base.ts (core) and the merged test that specs import as @fixtures
  data/            TestConstants.ts, factories
  utils/           assertion helpers, HTTP checks, env placeholders
  globalSetup.ts
tests/             api/<area>/*.spec.ts, ui/<area>/*.spec.ts
test-scenarios/    api/<area>/*.yml, ui/<area>/*.yml, and the schema
bugs/              <ID>.yml, and the schema
scripts/           the validator, the step-title check, the page inspector
docs/framework/    conventions.md (the rules) and an index of the longer docs
simpright.yml      the project config the scripts read (tests never do)
```

## Commands

```bash
npm test                          # both projects
npm run test:api                  # API project only
npm run test:ui                   # UI project only
npm run test:smoke                # the tests tagged @smoke
npx playwright test -g "UI-001:"  # one test by scenario ID
npm run typecheck                 # tsc --noEmit
npm run validate:scenarios        # scenario and bug files, IDs and specs
npm run check:step-titles         # step titles vs scenario steps, from a JSON report
npm run inspect:ui -- / --logged-out   # the page inspector
npm run report                    # open the last HTML report
```

## Versions

The template is versioned with git tags, starting at `v0.1.0`; `template` in `simpright.yml` records the version your core files came from. SimpRight is built on it: a check there fails when its core files differ from the template's.

More is planned as optional parts: a portal on GitHub Pages with the run history, test cases and bugs; CI publishing; and Claude Code agents that write contracts, scenarios and specs. [SimpRight](https://github.com/Zaytsman/SimpRight) shows all of them working today.

## License

[MIT](LICENSE)
