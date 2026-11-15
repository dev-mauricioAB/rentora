# Implementation Plan: Aggregate Property Listings

**Branch**: `001-aggregate-property-listings` | **Date**: 2026-11-15 | **Spec**: [spec.md](./spec.md)

**Input**: Feature specification from `/specs/001-aggregate-property-listings/spec.md`

## Summary

Deliver a read-only property discovery experience for renting or buying homes and apartments in
the 22 municipalities of the Região Metropolitana de Florianópolis (RMF). Visitors start from a
globe-style map with Brazil and Florianópolis selected by default, select an RMF municipality,
filter one factual listing list by transaction type, property type, and available neighborhood,
and open a details modal with source links and status history. Phase 1 has no accounts, comments,
ratings, overall scores, property pins, or exact addresses.

Use the requested pnpm monorepo with a React PWA and NestJS API, organized as a modular
application with explicit domain, use-case, presentation, and infrastructure boundaries. Provide
a Docker Compose workflow to build and validate the web, API, and local database together. No
candidate listing source currently has a verified permission path for collecting and reusing its
data. Source integrations and public launch are gated on documented permission; no scraping or
access-control bypass is planned.

## Technical Context

**Language/Version**: TypeScript; Node.js 24 LTS is the planning baseline. Revalidate supported
versions and pin the latest compatible stable TypeScript release when scaffolding.

**Primary Dependencies**: pnpm workspaces; React 19+, Vite, Tailwind, shadcn/ui, Zustand,
TanStack Query, React Router, Zod, and React Hook Form; NestJS and dotenv for local configuration;
Prisma ORM's latest stable GA release; Docker Engine and Docker Compose v2 for local stack validation.
MapLibre GL JS is the recommended map-rendering candidate. The user-preferred `svg-maps` package
does not provide the RMF municipal boundary data or a full map engine; see
[research.md](./research.md).

**Storage**: PostgreSQL through Prisma ORM. Prisma Postgres is a preferred evaluation candidate;
the production provider is not selected. Compare its region, latency, limits, and cost with Fly.io
Managed Postgres before provisioning.

**Testing**: TDD through red-green-refactor for new/changed behavior and bug fixes. Use Jest
across web/API/shared packages, Testing Library for React components, and NestJS test utilities
for API tests. Use an isolated local/disposable PostgreSQL database for integration tests.
Playwright covers only the named critical discovery/filter, detail/source, and recovery
journeys, selected by `@critical`; it does not replace participant usability or manual
accessibility review. See [ADR-001](./adr-001-testing-strategy.md).

Jest runs independently of Vite's plugins. Scaffolding must pin compatible stable transforms,
DOM/test utilities, and verify TypeScript/TSX, ESM, aliases, and NestJS decorators. Keep type
checks separate and do not introduce a second unit runner or bypass dependency conflicts.

**Agent quality guidance**: Repository-scoped Copilot instructions route frontend work through
Impeccable and reader-facing prose through Humanizer. Installed NestJS, TanStack Query,
Playwright, and React skills support their matching implementation/review tasks. These are
authoring tools, not runtime dependencies or substitutes for tests. Preserve feature
requirements, factual provenance, and
Spec Kit artifact structure. Read [PRODUCT.md](../../PRODUCT.md) and the approved
[frontend brief](./frontend-brief.md). Scaffolding may proceed, but visual direction and final
composition require approval before user-facing UI code. See [agent tooling](../../.github/README.md).

**UI component foundation**: The maintainer selected shadcn/ui on 2026-11-15. Use its
source-distributed components with Tailwind for common controls and modal behavior. This fits
the planned stack and allows Rentora-specific source/date and status-history presentation.
Material UI was considered for its comprehensive packaged components; combining its styling
system with Tailwind would add conventions to manage. Building all controls from scratch would
add accessibility and interaction maintenance without a demonstrated Phase 1 need.

The trade-off is ownership: imported shadcn/ui code becomes repository code, so upstream fixes
must be reviewed and integrated alongside local changes. Keep reusable controls in the web
presentation layer; do not add a shared UI package without a concrete reuse need. Domain rules,
shared contracts, and API code must not depend on UI components. Import only required controls,
reuse approved variants, and avoid combining shadcn/ui and Material UI in Phase 1.

At scaffolding, verify compatible React/Tailwind releases, choose one supported primitive
implementation, and pin dependencies. Record the source revision of imported components so
updates can be reviewed. No components are installed yet. The choice approves neither a default
shadcn theme nor a palette, font, Figma kit, or final composition. See the official
[shadcn/ui introduction](https://ui.shadcn.com/docs),
[theming guidance](https://ui.shadcn.com/docs/theming), and
[MUI Tailwind integration](https://mui.com/material-ui/integrations/tailwindcss/tailwindcss-v4/).

**Localization**: Initially ship Brazilian Portuguese (`pt-BR`) only. Isolate UI messages using
stable keys and format dates/numbers through locale-aware helpers so another locale and a
language switcher can be added later without rewriting components. Additional translations and
the visible switcher are deferred. Choose the smallest compatible localization approach during
implementation planning; source facts must retain their original meaning and provenance.

**Accessibility**: WCAG 2.2 AA, including keyboard-equivalent municipality selection and
accessible modal focus behavior. Combine automated checks with manual keyboard and
screen-reader validation; see [frontend brief](./frontend-brief.md).

**Target Platform**: Installable, responsive browser PWA; NestJS API deployed to Fly.io. PWA
installation is in scope; offline access to listing data is not specified.

**Project Type**: pnpm monorepo with a React web app, a NestJS API, and shared domain and contract
packages. Keep the API a modular monolith for Phase 1.

**Performance Goals**: No numeric latency, concurrency, or uptime goal is defined in the spec.
Measure the initial location-to-results flow and set service targets after the authorized data
source and hosting regions are known.

**Constraints**: Guest-only and read-only; only authorized source data; source attribution and
freshness must be visible; no exact property location or overall score; map selection is limited
to the official RMF municipalities. Docker Compose must support a reproducible local web/API/
database build without committing credentials. Map-boundary licensing, listing-source permission,
and production tile-provider terms must be verified before public launch.

**Scale/Scope**: Phase 1 covers the RMF only: nine core municipalities and thirteen expansion-area
municipalities. National or international expansion is a later phase. Expected listing volume and
traffic are not yet known.

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- **User value and correctness**: PASS. The spec defines guest user journeys, acceptance
  scenarios, and measurable outcomes for discovery, filters, source attribution, and details.
- **Evidence and clarification**: PASS. The design distinguishes verified facts from unknowns.
  Exact locations and overall scores are explicitly excluded; no property data may be guessed.
- **Architecture and Clean Code**: PASS. Use a fit-for-purpose modular monolith and explicit
  boundaries; do not add microservices or framework-specific abstractions without a need.
- **Security, privacy, and authorized data**: PASS FOR DESIGN. The source-ingestion boundary
  rejects unapproved sources; exact addresses and coordinates are not exposed.
- **Verifiable quality**: PASS FOR DESIGN. ADR-001 requires Jest tests before behavior
  implementation and a critical-only Playwright suite. Test map scope, filters, empty results,
  listing details, source attribution, status history, and unavailable data. Test runners and
  red/green evidence remain implementation work.
- **Reliable delivery**: PASS. Source failures and stale or missing data are represented
  explicitly, never as success-shaped or current facts.
- **Local validation**: PASS. The planned Compose stack makes the build and integrated local
  services reproducible; local credentials remain untracked and separate from production secrets.

**Gate outcome**: PASS for planning and a fixture-backed UX prototype. Implementation of real
source ingestion and public launch are BLOCKED until at least one source's access and reuse
permissions, the municipal-boundary data license, and a production map-provider plan are
documented. Do not treat the absence of an approved source as permission to scrape.

## Project Structure

### Documentation (this feature)

```text
specs/001-aggregate-property-listings/
├── plan.md
├── research.md
├── data-model.md
├── frontend-brief.md
├── adr-001-testing-strategy.md
├── contracts/
│   ├── public-api.md
│   └── source-ingestion.md
├── quickstart.md
└── tasks.md             # Created by /speckit-tasks
```

### Source Code (repository root)

```text
pnpm-workspace.yaml
.dockerignore
docker/
├── docker-compose.yml
├── api.Dockerfile
├── web.Dockerfile
└── .env.example             # Template only; local .env.docker is not committed
apps/
├── web/
│   ├── src/
│   │   ├── app/                 # App shell, routing, and composition
│   │   ├── presentation/        # Pages and reusable UI components
│   │   ├── use-cases/           # UI-facing application flows
│   │   ├── ports/               # Interfaces consumed by use cases
│   │   ├── infrastructure/      # API clients and query adapters
│   │   └── stores/              # Small client-only Zustand state
│   └── tests/
└── api/
    ├── src/
    │   ├── modules/             # areas, listings, and approved sources
    │   └── shared/
    └── test/
packages/
├── domain/                      # Framework-independent domain rules and types
├── contracts/                   # Shared Zod request/response contracts
└── config/                      # Shared TypeScript and lint configuration
```

**Structure Decision**: Use two deployable apps and shared packages in a pnpm workspace. The
web app follows presentation/use-case/port/infrastructure boundaries; the API is a NestJS modular
monolith. Keep domain types framework-independent and share only stable API contracts. This
adapts the layer boundaries observed in the user-provided reference project without copying its
code or adding a separate package for every feature.

## Test-first implementation requirements

Before story work, configure working Jest projects, React/NestJS test utilities, isolated
integration fixtures, critical-only Playwright, and the root pnpm scripts defined in
[ADR-001](./adr-001-testing-strategy.md). Include CI type/build/unit/integration/critical-browser
checks and preserve Docker Compose validation.

Generated tasks MUST include failing-test prerequisites for each behavior implementation,
Jest rule/contract coverage, CF-01 through CF-03 browser checks, and manual usability/accessibility
review. A valid red result must demonstrate the intended behavior failure, not broken tooling.
Do not generate an exhaustive browser suite or defer behavior tests to a final polish phase.
No `tasks.md` or application test scripts have been created yet.

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

No constitution violations are proposed. A modular monolith is sufficient for Phase 1.
