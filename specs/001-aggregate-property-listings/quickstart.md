# Quickstart and Validation Guide

**Status**: Validation guide for the planned application. This workspace currently has no
application manifests or runnable web/API implementation; the commands below become usable after
the project is scaffolded.

## Prerequisites

- Node.js 24 LTS (or a newer supported LTS after compatibility is revalidated).
- pnpm workspace dependencies installed.
- Docker Engine/Desktop and Docker Compose v2.
- A local or disposable PostgreSQL database for integration validation.
- Licensed RMF municipal boundary geometry and a map tile provider approved for the environment.
- For any live listing data, at least one source with documented collection and reuse permission.

Do not place database credentials or private keys in source control. Use local environment
variables for development and Fly.io secrets for deployment.

## Start the Planned Application

After scaffolding the workspace and its scripts:

### Docker Compose (integrated build)

```powershell
Copy-Item docker\.env.example docker\.env.docker
# Edit docker\.env.docker with local-only values; do not commit it.
docker compose -f docker\docker-compose.yml --env-file docker\.env.docker up --build -d
docker compose -f docker\docker-compose.yml ps
```

The planned Compose stack builds and runs Postgres, the API, and the web app. Wait for the
database health check before starting the API; use the Compose service name in the API's database
URL and a localhost URL for the browser-facing API base URL. Apply local database migrations using
the documented API command after startup. Stop services with:

```powershell
docker compose -f docker\docker-compose.yml down
```

The database uses a named volume, so stopping the stack does not remove local data. Do not use
`down --volumes` unless intentionally deleting that data.

### Host development

```powershell
pnpm install
pnpm dev
```

Open the local PWA URL printed by the web development server. Until a source passes the approval
gate, use clearly labeled test fixtures only; do not connect to candidate listing sites.

## Docker Build Validation

Once the Dockerfiles and Compose configuration exist, validate the production-like local images
and service startup with:

```powershell
docker compose -f docker\docker-compose.yml --env-file docker\.env.docker build
docker compose -f docker\docker-compose.yml --env-file docker\.env.docker up -d
docker compose -f docker\docker-compose.yml ps
docker compose -f docker\docker-compose.yml logs --follow web api
```

Expected results: all services build; Postgres becomes healthy; the API connects to the database;
the web app loads in a browser using the published localhost ports. The API and web images use
multi-stage builds. `.dockerignore` excludes dependency/build output and local secrets;
`docker/.env.example` is committed as a template, while `docker/.env.docker` remains local-only.
The production image must not run schema migrations implicitly at startup.

## User-Flow Validation

1. Open the globe view. Brazil and Florianópolis are selected as the default market.
2. Select an RMF municipality. No municipality outside the Phase 1 region is selectable.
3. Select rent or sale and house or apartment; optionally filter by a source-supported
   neighborhood.
4. Confirm the results remain one flat list and the empty state is clear when no fixture matches.
5. Review a property card and open its detail modal.
6. Confirm the card/modal show only source-attributed factual highlights, source link, known
   freshness/status history, and municipality/neighborhood when available.
7. Confirm no overall score, rating, exact address, or property coordinates are displayed.
8. Confirm no listing from an unapproved source appears.

## Automated Validation

After the corresponding scripts and tests exist:

```powershell
pnpm test
pnpm test:e2e
pnpm build
```

Expected coverage includes area scope, all rent/sale and house/apartment combinations,
neighborhood filtering, no-result behavior, source attribution, stale/unknown status, detail-modal
content, and the absence of scores and exact locations. E2E tests may use synthetic fixtures;
they must not scrape real listing sources.

## Release Gate

Do not publish live listings until the source-approval preconditions in
[source-ingestion.md](./contracts/source-ingestion.md) and the municipal-boundary license have
been verified. Recheck map-provider commercial terms, quotas, attribution, and availability
before production.
