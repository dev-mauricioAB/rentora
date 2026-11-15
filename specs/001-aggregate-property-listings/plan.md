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

**Primary Dependencies**: pnpm workspaces; React 19+, Vite, Tailwind, Zustand, TanStack Query,
React Router, Zod, and React Hook Form; NestJS and dotenv for local configuration; Prisma ORM's
latest stable GA release; Docker Engine and Docker Compose v2 for local stack validation.
MapLibre GL JS is the recommended map-rendering candidate. The user-preferred `svg-maps` package
does not provide the RMF municipal boundary data or a full map engine; see
[research.md](./research.md).

**Storage**: PostgreSQL through Prisma ORM. Prisma Postgres is a preferred evaluation candidate;
the production provider is not selected. Compare its region, latency, limits, and cost with Fly.io
Managed Postgres before provisioning.

**Testing**: Vitest and Testing Library for web/shared packages; NestJS test utilities for API
unit and integration tests; Playwright for end-to-end map, filtering, listing-detail, and PWA
smoke tests. Use a disposable or local PostgreSQL database for integration tests.

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
- **Verifiable quality**: PASS. Test the map scope, filters, empty results, listing details,
  source attribution, status history, and unavailable data.
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

## Complexity Tracking

> **Fill ONLY if Constitution Check has violations that must be justified**

No constitution violations are proposed. A modular monolith is sufficient for Phase 1.
