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
3. Set it up for your app with [Claude Code](https://claude.com/claude-code): open the folder in the Claude desktop app, or run `claude` in it, and type `/simpright-setup`. It asks about your app, the layers you test, the login, the parts you want and your GitHub repository, then runs `simpright init` and `simpright apply`, each after you've seen its changes. The skill is in `.claude/`, so there's nothing to install and no folder to trust.

## What you get

- **The core**, 23 files listed under `core` in `simpright.yml`: the config and environments, `BaseClient` and `TokenService`, `BasePage`, `BaseComponent` and `BaseDialog`, the base fixtures (role, login, cleanup), assertion messages with masking, `fillSecret` (types a secret into a field without printing it in the report), the HTTP checks and send helpers, `globalSetup`, the page inspector, `playwright.config.ts` and the framework's conventions (`docs/framework/conventions.md`). A core file imports only other core files, the two seams and packages, and `npm run doctor` checks it.
- **Two seams for your app:** `src/config/project.ts` (the test-id attribute, the login, what runs before and after a run) and `src/config/roles.ts`.
- **Examples:** an API spec (`GET /products/search`, 5 tests) and a UI spec (product search, 5 tests), each with its scenario file, plus the client, service, page object, component and precondition fixture they use.
- **Scenarios and bugs:** YAML files in the formats of [`@simpright/scenarios`](https://github.com/Zaytsman/SimpRightKit/tree/main/packages/scenarios), one of the [SimpRight Kit](https://github.com/Zaytsman/SimpRightKit)'s npm packages: `npm run validate:scenarios` checks the scenario and bug files, the IDs, and that every spec matches its scenarios; `npm run check:step-titles` checks the step titles of a run.
- **The setup:** `/simpright-setup` and `/simpright-doctor`, Claude Code skills from the kit's setup plugin. The setup interviews you and runs the kit's commands at your OK; the doctor explains what `npm run doctor` finds and checks the GitHub settings the parts need.
- **The project check:** `npm run doctor` (the kit's `simpright` package) checks `simpright.yml` and how its parts fit together, the workflows' Node version, what the core files import, the files `npm run apply` writes, and that the core files equal the template's at the version `simpright.yml` records. `npm run apply` writes what the parts in `simpright.yml` need: here the setup's skills, and with more parts the CI workflows, the QA agents and the kit's packages.
- **The page inspector:** `npm run inspect:ui -- <path>` prints a page's `data-test` elements and accessibility tree, read-only, for writing page objects and scenarios.
- **CI:** `verify` (typecheck, the validator and `doctor`) and `Tests` (both projects, the report kept as an artifact) on pull requests and pushes to `main`; Dependabot; every action pinned to a commit SHA. The kit's CI part replaces them when you turn it on: generated workflows with scheduled runs and the portal on GitHub Pages.

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

`/simpright-setup` does this with you. By hand:

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
test-scenarios/    api/<area>/*.yml, ui/<area>/*.yml
bugs/              <ID>.yml (README.md shows the format)
scripts/           the page inspector
docs/framework/    conventions.md (the rules) and an index of the longer docs
.claude/           the kit's Claude Code skills (and its agents, with the agents part), written by npm run apply
.simpright/        state.json: what npm run apply wrote
simpright.yml      the project config the kit's commands and the agents read (tests never do)
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
npm run doctor                    # simpright.yml, the core's imports, the core vs the template (reads GitHub)
npm run apply -- --dry-run        # what simpright.yml's parts need; without --dry-run, written
npm run inspect:ui -- / --logged-out   # the page inspector
npm run report                    # open the last HTML report
```

## Versions

The template is versioned with git tags, starting at `v0.1.0`; `template` in `simpright.yml` records the version your core files came from, and `npm run doctor` compares them with that version's (it reads them from GitHub). The template's own `verify` runs it with `--offline`, because the template is its own reference. SimpRight is built on it: its `doctor` fails when its core files differ from the template's.

The scenario validator and its schemas, `doctor` and `apply` come from the kit's npm packages, which move together: one Dependabot group bumps them, and after a kit upgrade `npm run apply` brings the skills and workflows it writes up to date. The kit's other parts are off in the template, and `/simpright-setup` turns on the ones you want: CI that runs the tests on a schedule and publishes them to the portal on GitHub Pages (the run history, test cases and bugs), a wiki, and the QA agents that write contracts, scenarios and specs. [SimpRight](https://github.com/Zaytsman/SimpRight) shows all of them working.

## License

[MIT](LICENSE)
