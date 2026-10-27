# Framework docs

`conventions.md` here holds the framework's rules, and `CLAUDE.md` imports it. The longer explanations it points to
(`docs/framework/<NN>-*.md`) are in [SimpRight](https://github.com/Zaytsman/SimpRight), the project this template comes
from, for now. They describe SimpRight's own tests, but what they say about the framework's core applies here as it is:

| Doc | What it covers |
|---|---|
| [01 Architecture](https://github.com/Zaytsman/SimpRight/blob/main/docs/framework/01-architecture.md) | The layers, the folders, what happens during a run, the design principles |
| [02 Configuration](https://github.com/Zaytsman/SimpRight/blob/main/docs/framework/02-configuration.md) | Environments, secrets, `env.ts`, `playwright.config.ts`, projects, users created per run, login modes |
| [03 Fixtures](https://github.com/Zaytsman/SimpRight/blob/main/docs/framework/03-fixtures.md) | The fixture tree: shared, API and UI fixtures, precondition fixtures, cleanup, what `@fixtures` exports |
| [04 API layer](https://github.com/Zaytsman/SimpRight/blob/main/docs/framework/04-api-layer.md) | `BaseClient`, clients, services, DTOs, tokens, error handling, the coverage hook |
| [05 API specs](https://github.com/Zaytsman/SimpRight/blob/main/docs/framework/05-api-specs.md) | How an API test is written: steps, shared checks, step helpers, templates, attachments |
| [06 UI layer](https://github.com/Zaytsman/SimpRight/blob/main/docs/framework/06-ui-layer.md) | Pages, components, dialogs, flows, waits |
| [07 UI specs](https://github.com/Zaytsman/SimpRight/blob/main/docs/framework/07-ui-specs.md) | How a UI test is written: preconditions, setup helpers, steps and checks |
| [08 Test data](https://github.com/Zaytsman/SimpRight/blob/main/docs/framework/08-test-data.md) | Seeded constants, factories, unique names, throwaway users, cleanup, shared-data safety |
| [09 Scenarios and bugs](https://github.com/Zaytsman/SimpRight/blob/main/docs/framework/09-scenarios-and-bugs.md) | Scenario files, bug files, `simpright.yml`, the validator, the step-title check, the smoke set |
| [10 Reporting and secrets](https://github.com/Zaytsman/SimpRight/blob/main/docs/framework/10-reporting-and-secrets.md) | Reporters, attachments, assertion messages, masking, what never reaches a report |

SimpRight's other docs cover parts this template doesn't have yet: CI publishing and the portal, and the AI agents.
