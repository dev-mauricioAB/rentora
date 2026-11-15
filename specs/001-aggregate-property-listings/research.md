# Research and Decisions: Aggregate Property Listings

**Date**: 2026-11-15
**Feature**: [spec.md](./spec.md)

## Summary

The user-facing flow is clear enough to design, but live data ingestion is not ready to implement:
no candidate property source has an established permission path for the intended collection and
reuse. Build a provider-neutral design and fixture-backed experience only. Real ingestion and
public launch remain gated on source permission, licensed municipal boundary data, and an
approved production map provider.

## Product and Reference Flow

The user-provided [Politicômetro](https://www.politicometro.org/) reference was inspected on
2026-11-15, including its [Florianópolis map view](https://www.politicometro.org/br?uf=sc&cidade=4205407)
and a [detail page](https://www.politicometro.org/p/topazio).

Observed patterns relevant to Rentora:

- Navigation narrows from globe/country to region and city, with visible location context.
- Selecting a city leads to a city-level summary and a set of organized details.
- Detail pages use a short factual summary followed by topic sections; sources and update dates
  appear with the relevant data.
- Missing data is explained rather than silently omitted or replaced with a guess.
- The reference explicitly avoids an overall score or ranking.

Rentora's confirmed adaptation is a globe-to-Brazil-to-Florianópolis/RMF flow, one factual
property list, neighborhood filters when supported, and a listing-details modal. The map is for
area discovery, not exact property pins. No external assets or implementation are to be copied.
The reference has a text location search, but this feature spec does not currently require one.

## Região Metropolitana de Florianópolis

The official Santa Catarina Assembly statute page for [Complementary Law 495/2010](https://leis.alesc.sc.gov.br/html/2010/495_2010_Lei_complementar.html)
redirects to its current law record. Article 5 lists nine municipalities in the RMF core and
thirteen in its expansion area. Amendments listed by the statute page were reviewed through
[LC 860/2024](https://leis.alesc.sc.gov.br/html/2024/860_2024_lei_complementar.html); the
amendments reviewed do not change RMF Article 5.

**Core municipalities (9)**: Águas Mornas, Antônio Carlos, Biguaçu, Florianópolis, Governador
Celso Ramos, Palhoça, Santo Amaro da Imperatriz, São José, and São Pedro de Alcântara.

**Expansion-area municipalities (13)**: Alfredo Wagner, Angelina, Anitápolis, Canelinha, Garopaba,
Leoberto Leal, Major Gercino, Nova Trento, Paulo Lopes, Rancho Queimado, São Bonifácio, São João
Batista, and Tijucas.

Other official amendment records reviewed: [LC 523/2010](https://leis.alesc.sc.gov.br/html/2010/523_2010_Lei_complementar.html),
[LC 571/2012](https://leis.alesc.sc.gov.br/html/2012/571_2012_Lei_complementar.html),
[LC 580/2012](https://leis.alesc.sc.gov.br/html/2012/580_2012_Lei_complementar.html), and
[LC 640/2015](https://leis.alesc.sc.gov.br/html/2015/640_2015_Lei_complementar.html).
Recheck the law before implementation. This is a product-scope reference, not legal advice.

The statutory membership is verified; the license and technical format for a municipal-boundary
geometry dataset are not. That separate boundary-data dependency remains a pre-implementation
gate.

## Listing Source Access and Reuse

The sources named by the user are candidates only. Research was not legal advice and did not
establish a permission to collect, store, or republish their listing data.

| Candidate | Findings at research time | Decision |
|---|---|---|
| Google | The [Maps Platform terms](https://cloud.google.com/maps-platform/terms) describe a maps/places/geocoding service, not a residential listing feed. Terms restrict scraping and exporting Maps content for use outside the service. No Google property-listing API was found. | Do not use Google Maps content as a property-listing database. Verify any separate, authorized feed before use. |
| Airbnb | The [API terms](https://www.airbnb.com/help/article/3418) and [general terms](https://www.airbnb.com/help/article/2908) were reviewed. Research did not find a public self-serve listing-reuse program and found restrictions on scraping and building copies/databases. | No integration until Airbnb explicitly authorizes the specific access and reuse. |
| Facebook public groups | The [Meta Platform Terms](https://developers.facebook.com/terms/) and groups documentation were consulted, but access and reuse permissions for public group listings were not established. | Treat as unavailable until Meta's applicable permissions and terms are confirmed. |
| QuintoAndar | The [QuintoAndar terms](https://www.quintoandar.com.br/termos) were reported to prohibit scraping/extraction and content copying; no public developer API was found. Its [robots.txt](https://www.quintoandar.com.br/robots.txt) is a crawl signal, not permission. | Do not scrape or copy. Require an authorized API, agreement, or other verified permission. |

**Decision**: Keep each candidate source out of ingestion and presentation until its permitted
access method, reuse scope, attribution requirements, and retention conditions are documented.
The first live source must pass that gate before public listing data is shown. Until then, use
clearly labeled synthetic fixtures only in local or controlled UX validation.

## Map Rendering, Tiles, and Boundary Data

Map rendering and geographic boundary data are separate dependencies.

### Map renderer and tile provider

- [MapLibre GL JS](https://maplibre.org/maplibre-gl-js/docs/examples/display-a-globe-with-a-fill-extrusion-layer/)
  is an open-source renderer with a documented globe projection. It does not provide its own
  basemap data or RMF municipal boundaries.
- [MapTiler Cloud pricing](https://www.maptiler.com/cloud/pricing/) describes a Free plan for
  testing, prototyping, research, and non-commercial use, with monthly map-session and request
  quotas. The Free plan suspends maps when limits are exceeded. The [Cloud terms](https://www.maptiler.com/terms/cloud/)
  limit Free use to non-commercial use and R&D, so it is not an appropriate assumption for
  commercial production. The Flex plan was listed at $30/month at research time; pricing and logo
  requirements must be rechecked before choosing it.
- [MapTiler attribution requirements](https://www.maptiler.com/copyright/) require visible map
  attribution. Browser-visible API keys must be restricted to allowed origins.
- [OpenFreeMap](https://openfreemap.org/) states that its public vector tiles may be used
  commercially without a key or view/request limit, but it has no SLA and is donation-funded.
  Attribution to OpenMapTiles and OpenStreetMap is required. This is a low-cost prototype option,
  not a guaranteed-availability commitment.
- The [OpenStreetMap tile policy](https://operations.osmfoundation.org/policies/tiles/) says its
  standard tile service is best-effort and not suitable as a production app's default tile server.

**Recommendation**: Use MapLibre as the renderer behind a small provider boundary. Evaluate
OpenFreeMap for early validation because the map is an area selector, not the product's core data
service. Do not use MapTiler Free for public commercial production. Reconfirm a production
provider, terms, budget, attribution, and availability expectations before launch.

### SVG maps and RMF geometry

- [Victor Cazanave's `svg-maps` Brazil package](https://github.com/VictorCazanave/svg-maps/tree/master/packages/brazil)
  supplies Brazil state shapes, not RMF municipality polygons. It is data, not a full map engine,
  and is licensed CC BY 4.0 with attribution.
- The associated `react-svg-map` wrapper was reported as old and declaring React 16 peer
  requirements, so it is not a suitable default for a React 19+ app.
- A globe and city-scale municipality selection need geometry for the 22 official RMF
  municipalities. IBGE municipal mesh data is a candidate, but its license, exact downloadable
  format, and update process were not established in this research.

**Decision**: Do not assume `svg-maps` alone can implement the selected scope. Keep municipal
geometry as a separately licensed asset, and verify its source and redistribution rights before
including it. No property coordinates or markers are required.

## Preferred Stack and Runtime

The user-provided stack remains a preference, not an immutable requirement. Current compatibility
research supports these planning directions:

- Use a single supported Node.js LTS line across the workspace; Node 24 LTS was the best-fit
  candidate at research time. Pin the exact patch version only when scaffolding.
- React 19+, Vite, Tailwind, Zustand for client-only UI state, TanStack Query for server state,
  React Router, React Hook Form, and Zod align with the requested web app. Verify exact peer
  compatibility before locking versions.
- The `svg-maps` dataset does not cover the municipal boundary requirement. A MapLibre renderer
  plus independently licensed boundary data is a better candidate for the selected globe and
  region-selection flow.
- Use NestJS as one API application organized by feature modules. Use dotenv for local
  configuration only; deployment secrets belong in Fly.io secrets.
- Prisma ORM 8 was an RC in registry/release-status research; Prisma ORM 7 was the latest stable
  GA line. Interpret “latest Prisma” as latest stable GA, not an RC, and recheck when scaffolding.
- Use PostgreSQL. Prisma Postgres is a portable PostgreSQL service; its Free tier was listed as
  200,000 operations/month and 2 GB storage. The [Prisma pricing page](https://www.prisma.io/pricing)
  may change, and the region/latency relationship to Fly.io was not verified.
- [Fly.io Managed Postgres](https://docs.fly.io/postgres/) lists a Basic plan at $38/month plus
  storage (as of research); same-region app/database traffic has no transfer charge. This is
  operationally convenient but materially more expensive than a free-tier prototype.

**Decision**: Keep the data layer PostgreSQL/Prisma-compatible and avoid provider-specific schema
features. Defer the production database host until region, cost, backups, and service-source
volume are known. Do not provision resources in this planning phase.

## Monorepo Structure Reference

The user-provided local reference was inspected read-only. Its useful patterns are a pnpm
workspace with `apps/web` and `apps/api`, shared domain/config packages, and frontend
presentation/use-case/port/infrastructure/store boundaries. The API groups NestJS features into
modules. Rentora will use those boundary ideas, not copy its code or add its Turborepo setup
without a demonstrated need.

## Local Docker Workflow Reference

The user's Zelo reference was inspected read-only for `docker/docker-compose.yml`, the API and web
Dockerfiles, `docker/.env.example`, and `.dockerignore`. Its reusable patterns are a Compose stack
for Postgres, API, and web; a database health check and named data volume; separate multi-stage
web/API image builds from the workspace root; environment variables passed through an example
file; and a Docker ignore list that excludes local secrets and build output. Its exact credentials,
application commands, image names, and Turborepo-specific build steps are not Rentora defaults and
were not copied.

**Decision**: Add a local Docker Compose build-and-run workflow for Rentora's web, API, and
PostgreSQL services. Use a tracked `docker/.env.example` and an ignored local `docker/.env.docker`;
never commit credentials. Compose should wait for database health before API startup, use the
Compose service name for container-to-container database connections, and publish the web/API on
localhost for browser access. Keep migration execution explicit in local workflow documentation
and out of the production container's default startup command. Use multi-stage builds and a
root-level `.dockerignore`; choose package scripts and image details during implementation rather
than copying Zelo-specific commands.

## Decisions and Deferred Items

| Topic | Planning decision | Gate or follow-up |
|---|---|---|
| Web/API layout | pnpm monorepo; React PWA plus NestJS modular monolith | Confirm exact package scripts and dependency versions during scaffolding |
| Local validation | Docker Compose builds and runs web, API, and PostgreSQL together | Implement multi-stage images, health checks, safe env templates, and documented build/start/stop commands |
| Listing sources | No source selected; candidates remain excluded | Verify at least one source's permission, data fields, attribution, and retention before ingestion |
| Area boundary | Use the current statutory 22-municipality RMF list | Recheck law and choose a licensed geometry dataset before implementation |
| Map renderer | MapLibre is the recommended candidate; keep provider switchable | Test globe-to-RMF transition and browser performance |
| Map tiles | OpenFreeMap is a low-cost validation candidate; MapTiler Free is not production-commercial | Approve production provider, terms, attribution, reliability, and budget |
| Listing locations | Municipality/neighborhood only; no exact address or coordinates | Do not ingest/display exact locations in Phase 1 |
| Rating | No overall score or ranking | Show only source-attributed factual highlights |
| Database host | PostgreSQL through Prisma; provider not selected | Compare Prisma Postgres and Fly Managed Postgres for region, cost, backups, and latency |
| PWA offline behavior | Installable shell; no offline listing guarantee | Add offline listing support only through a separate requirement |
