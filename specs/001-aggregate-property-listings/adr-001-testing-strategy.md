---
artifact: adr
version: "1.0"
created: 2026-11-15
status: accepted
---

# ADR-001: TDD with Jest and critical-flow Playwright tests

## Status

Accepted by the Rentora maintainer on 2026-11-15.

## Context

Rentora's Phase 1 requirements include geographic restrictions, draft/applied filters,
source permission, dated observations, missing facts, and accessible modal return behavior.
These rules need focused regression tests before implementation. The maintainer selected
test-driven development (TDD), Jest for unit tests, and Playwright for critical user flows.
This replaces the earlier Vitest planning choice for web/shared packages.

The repository currently contains planning documents and agent tooling, not a runnable app.
This decision defines implementation obligations; it does not establish that any application
test, dependency combination, or browser journey has passed.

## Decision

### Test-first workflow

For new or changed behavior and bug fixes, use red-green-refactor:

1. Write a focused test derived from a requirement or regression.
2. Run it and confirm that it fails for the expected missing or incorrect behavior.
   A missing dependency, broken test loader, or compilation error is not a valid red result.
3. Implement the smallest change that makes it pass.
4. Refactor while keeping the relevant tests green; run affected type/build checks.

Use existing tests and helpers where possible. Documentation-only changes do not require
application tests. Do not create tests for trivial accessors, constants, or framework behavior
merely to satisfy TDD or a coverage percentage.

### Jest for unit, component, and integration tests

Use Jest across the web app, API, and shared packages. Use Testing Library for focused React
component behavior and `@nestjs/testing` for API dependency injection and module tests.
Keep domain tests independent of React, NestJS, Prisma, map engines, and a running database.
Use an isolated local/disposable PostgreSQL database for database integration tests; mock
external providers in unit tests. Do not contact candidate listing sources.

Jest runs separately from Vite's plugin pipeline. At scaffolding, select and pin compatible
stable Jest, TypeScript/TSX transforms, DOM environment, and test utility versions. Verify
ESM imports, workspace aliases, React rendering, and NestJS decorators with representative
tests. Keep type checking explicit: a transpiling test runner does not prove type safety.
Do not add Vitest as a second runner or bypass peer-dependency conflicts.

### Playwright only for critical browser flows

Tag the following browser journeys `@critical`. The default E2E script must select that tag;
new browser scenarios need a stated link to a critical journey. Prefer Jest for exhaustive
rule combinations and focused component tests.

| Flow | Browser coverage | Related requirements |
|---|---|---|
| CF-01: Guest discovery and filters | Default Brazil/Florianopolis context, in-scope municipality selection with a keyboard equivalent, draft edits causing no request, Apply, Reset requiring Apply, municipality changes retaining unapplied type drafts and clearing neighborhoods, and a clear no-match state. Include PWA entry/service-worker smoke checks in this journey. | FR-001, FR-011, FR-015, FR-016, FR-018, FR-019, FR-020; SC-002, SC-006, SC-007, SC-010, SC-013, SC-014 |
| CF-02: Details and source handoff | Open details, read source/date/history and missing-data states, reach long content with the fixed close header, then close with filters/list position preserved and focus restored. Open an available source link against a controlled fixture destination; explain unavailable links. | FR-004, FR-005, FR-006, FR-012, FR-014, FR-018, FR-021; SC-001, SC-003, SC-005, SC-008, SC-009, SC-013 |
| CF-03: Discovery recovery | Listing-request failure/retry, no-approved-data state, and map failure with the equivalent municipality control remaining usable. Recovery must not silently change applied criteria or invent available data. | FR-005, FR-007, FR-009, FR-011, FR-018, FR-020; SC-004, SC-013, SC-014 |

Use synthetic fixtures in controlled validation. Exercise the integrated local web/API stack
for the normal journeys; use controlled network interception for fault cases and external
fixture destinations. Do not scrape sources or depend on third-party uptime.
The map's unresolved composition, geometry license, and provider approval remain separate gates.
PWA smoke checks do not add offline listing persistence or guarantee device installation.

Automated browser tests check interactions and regressions; they do not establish usability
with participants or full WCAG 2.2 AA compliance. Retain maintainer usability review and manual
keyboard/screen-reader, text-resizing, and responsive checks. Do not suppress accessibility
rules or failures to obtain a passing result.

### Implementation and completion gates

Future `tasks.md` generation MUST include:

- Foundational Jest configuration and working unit/component/API tests, isolated integration
  setup, critical-only Playwright configuration, and pnpm scripts before dependent story work.
- Test-first tasks tied to each changed rule/acceptance scenario, with explicit dependencies
  from implementation tasks to their required failing tests.
- Jest coverage of all 22 allowed municipalities and out-of-scope rejection, all four
  transaction/property combinations, supported neighborhoods, filter-state transitions,
  unapproved-source exclusion, missing/stale/dated facts, location/score exclusions, and
  locale-sensitive contracts and presentation.
- CF-01 through CF-03 browser tasks, manual accessibility/usability review, and integrated
  Docker Compose validation. Do not mark blocked map/source work complete using placeholders.
- Non-watch CI checks for types/builds, Jest unit/integration suites, and the critical
  Playwright suite. Keep integration database setup explicit and credentials untracked.

The planned root scripts are `test` (Jest units/components), `test:watch` (opt-in Jest watch),
`test:integration` (isolated Jest integration), and `test:e2e`
(`playwright test --grep @critical`). These scripts do not exist yet.
During development, run the smallest relevant selection. Before integration, run the
affected suites and required CI/build checks. Report red/green results and any skipped or
blocked checks; do not claim success for tests that were not run.

## Consequences

### Positive

- One unit-test runner covers the workspace and fits NestJS testing utilities.
- Test-first rule coverage makes filter, provenance, and geographic regressions visible.
- A small browser suite protects critical journeys without testing every variant in E2E.

### Negative

- Jest requires a separate transform/resolution setup alongside Vite.
- Red-green evidence and isolated integration fixtures add initial setup work.
- Browser checks cannot replace participant feedback or manual accessibility review.

### Neutral

- Runner versions and package script implementations remain scaffolding work.
- Source permissions, final map/UI approval, and deployment gates remain unchanged.

## Alternatives Considered

### Vitest for web/shared packages and Jest for the API

Vitest integrates with Vite's pipeline, but splits unit-test configuration and conventions.
The maintainer chose Jest across the workspace instead.

### Tests after implementation

Post-implementation tests can miss the intended regression and do not meet the selected
test-first workflow.

### Broad Playwright coverage of every screen and rule combination

This would increase browser-suite runtime and maintenance. Use Jest for detailed rule
coverage and reserve Playwright for the named critical journeys.

## References

- [Feature specification](./spec.md)
- [Implementation plan](./plan.md)
- [Validation guide](./quickstart.md)
- [Constitution](../../.specify/memory/constitution.md)
- [Jest setup, TypeScript, and Vite limitations](https://jestjs.io/docs/getting-started)
- [NestJS testing](https://docs.nestjs.com/fundamentals/testing)
- [Playwright best practices](https://playwright.dev/docs/best-practices)
- [Playwright tags](https://playwright.dev/docs/test-annotations#tag-tests)
