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

## Agent Authoring Tools

**Decision**: Install [Impeccable](https://github.com/pbakaus/impeccable) for frontend work and
[Humanizer](https://github.com/blader/humanizer) for generated prose at repository scope. Route
them through Copilot's repository-wide and path-specific instructions; see
[agent tooling](../../.github/README.md) for installation, versions, and usage.

**Rationale**: Impeccable provides design playbooks and UI review guidance. Humanizer edits prose
for clarity without changing its meaning. They complement, rather than replace, focused tests,
rendered UI inspection, source attribution, and the project's no-guessing rules.

**Alternatives considered**: Links alone would depend on agents fetching guidance each time;
global installations would not travel with the repository. An application-level text-processing
service is not requested and would need separate runtime and provenance requirements.

Keep the upstream Apache-2.0 and MIT notices. Automatic hooks remain disabled pending review.
Humanizer must preserve Spec Kit structure and requirement semantics; Impeccable must not invent
branding, product facts, or a new scope. Product context and visual direction should be approved
before the first UI implementation.

## Public Mobbin Flow Research

Research date: 2026-11-15. This is inspiration from shipped products, not evidence that these
flows improve conversion, satisfy accessibility standards, or have been tested with Rentora users.

### Access and evidence limits

[Mobbin](https://mobbin.com/) describes a reference library of shipped screens and ordered flows.
Its public [explore page](https://mobbin.com/explore/mobile) exposes selected examples and asks
visitors to log in or join to continue browsing. Full search and flow access depend on the
account/plan; MCP access is advertised for paid plans. No account was created, no subscription
was purchased, and no access controls were bypassed.

After the user authenticated the shared browser, the supplied
[Glassdoor iOS catalog](https://mobbin.com/apps/glassdoor-ios-2e961328-3791-42e9-abbc-47bf48fe3505/d4041b83-7474-4794-ae05-90b515aab5c4/screens)
was accessible. It reported 390 screens and exposed three highlight previews. The
[flows tab](https://mobbin.com/apps/glassdoor-ios-2e961328-3791-42e9-abbc-47bf48fe3505/d4041b83-7474-4794-ae05-90b515aab5c4/flows)
reported 118 flows, but full catalog access still required an upgrade. The earlier
`/apps/glassdoor` 404 was an incorrect route, not evidence of catalog absence.
Only the three visible screenshots were inspected; no full search/filter/detail transition
sequence was verified.

The observations below distinguish a rendered screenshot from Mobbin's published flow
descriptions. Capture dates and current product versions were not available in the inspected
material. The Glassdoor collection selector said "Latest", but an exact capture date and app
version were not verified. Do not describe these as current live-product behavior.

### References and scope fit

| Reference | Evidence inspected | Relevant pattern | Rentora adaptation and exclusions |
|---|---|---|---|
| [Airbnb iOS map screen](https://mobbin.com/explore/screens/ff9ec8e7-5b5f-49b9-8053-30757e0d321c) | Rendered public screen: search/filter controls above a map, category strip, price pins, a selected property card, and a "See similar" action | Location context and a concise item preview can coexist; filters remain identifiable while exploring | Retain location/filter context with factual cards. Do not copy price pins, favorites, guest-favorite badges, star ratings, booking dates, or the recommendation action. Rentora selects municipalities and has one result list |
| [On: filtering products](https://mobbin.com/explore/flows/d33b48fe-e97b-4915-bb49-29e4afe9b716) | Public six-screen flow metadata and description: menu, category/activity/type filtering, then filtered listing | Named categories narrow a result set and return to a listing | Use rent/sale, house/apartment, and supported neighborhood filters. The description does not establish apply/reset semantics, focus behavior, or retained scroll position |
| [Turo: filtering cars](https://mobbin.com/explore/flows/4e15f54c-7a7f-4ac5-a54d-769a251f4b8f) | Public seven-screen flow metadata and description: vehicle type/year choices, then filtered car listing | Attribute selection leads to a filtered list | The structure supports Rentora's existing attribute filters. Do not add vehicle-style ranges, booking, payment, or extra property filters merely because another marketplace has them |
| [Nextdoor alert detail](https://mobbin.com/explore/screens/fffdd381-a09f-460e-838d-049314184180) | Public real-estate category listing labels an alert-detail screen with map/map-pin tags; screenshot not inspected | A comparison boundary, not an adopted pattern | Neighborhood/community alerts and exact pins do not match the read-only Phase 1 scope |
| [Glassdoor splash preview](https://mobbin.com/screens/7a0ecc3a-e568-433f-aab9-b0463f425c34) | Rendered highlight: wordmark on a green background | Brand introduction only; no discovery behavior visible | Does not establish a Rentora palette or justify an additional splash step |
| [Glassdoor home/results preview](https://mobbin.com/screens/08dbbd95-789e-409c-9084-37b51c13f9eb) | Rendered highlight: search entry, personalized greeting, stacked job cards with employer, role, location, compensation source label and relative age; ratings, bookmarks and bottom navigation also appear | Repeated card hierarchy makes attributes comparable; compensation provenance is visible beside the value | Use consistent factual card fields and visible source labels when data is permitted. Do not add personalized recommendations, ratings, bookmarks, community tabs, or estimated property values |
| [Glassdoor job-detail preview](https://mobbin.com/screens/ffc295c8-0b7c-43b4-9a6d-a3e31829c1f1) | Rendered highlight: item title and location, employer/rating, qualification section, AI resume-match prompt, back/share controls and prominent application action | Detail context remains identifiable and a next action is easy to find | Keep Rentora's municipality, listing identity, and original-source action clear inside its approved modal. Do not adopt resume matching, application, rating, share, or qualification features |

The public [real-estate category](https://mobbin.com/explore/mobile/app-categories/real-estate)
includes Airbnb and Nextdoor examples. Category membership alone is not a reason to adopt their
features. Mobbin's [Listing flows](https://mobbin.com/explore/mobile/flows/listing) page includes
creating a sell/give-away post; its title must not be mistaken for a read-only results-list flow.

### Additional housing references

The authenticated [iOS real-estate search](https://mobbin.com/search/apps/ios?content_type=apps&sort=popularity&filter=appCategories.Real+Estate)
returned nine apps: Airbnb, Nextdoor, Vrbo, Redfin, Opendoor, Trulia, Houzz, Compass, and Zillow.
These are catalog results, not an exhaustive market comparison or a usability ranking.
Zillow and Redfin are closer discovery references than Glassdoor because the inspected screens
show homes for sale. Full catalog access still required Pro. Some preview captures failed or
showed loading placeholders; those captures provide no evidence of app behavior.

| Reference | Evidence inspected | Useful comparison and limits |
|---|---|---|
| [Zillow map/results highlight](https://mobbin.com/screens/a0d749ba-675e-4f37-b6d7-98b5fe339adc) | Rendered screenshot: search field, filter control with a count badge, map with price pins, result count and transaction label in a bottom panel, plus saved-search and navigation actions | Study how selected criteria and result context remain visible during exploration. Rentora keeps its municipality map and one list; exclude property pins, saved searches, favorites, planning/inbox tabs, and school-related search scope. Opening filters, applying/resetting them, and returning from details were not observed |
| [Redfin map/results highlight](https://mobbin.com/screens/20316afb-d7e2-491d-9005-b5b1552cdf03) | Rendered screenshot: search/filter controls, price pins, a bottom results panel with transaction selector and result count, a partial card with a relative-age label, recommended ordering and saved-search action | Compare control visibility and the relationship between location context and results. Do not import exact pins, recommendations/rankings, saved searches, account tabs, or relative-age labels as a substitute for dated observations. The screenshot does not establish drawer gestures or filter behavior |
| [Redfin detail highlight](https://mobbin.com/screens/dd7ba1cf-8546-45e6-815f-3957d9c700e5) | Rendered screenshot: media, for-sale label, price, bedroom/bathroom/area summary, exact address, map thumbnail, share/favorite controls and showing-request action | Study the compact factual summary and clear next action. Rentora retains its modal, permitted facts with provenance, municipality/neighborhood only, and original-source action. Exact addresses, map thumbnails locating properties, showings, favorites, sharing and walkthrough media are not additions to Phase 1 |
| [Trulia catalog](https://mobbin.com/apps/trulia-ios-84ead256-a292-4554-a51e-79f7b5f08f91/c4e4a645-ac53-4b38-a3d5-7237724bb338/screens) | Catalog metadata reported 269 screens. Initial catalog output named Searching Trulia, Filtering listings and Listing detail flows; the inspected preview showed a registration/sign-in invitation, not discovery | A candidate for deeper search/filter/detail research, not evidence that those transitions work a particular way or that browsing requires an account. Rentora's guest entry remains unchanged |

Trulia's named [search](https://mobbin.com/apps/trulia-ios-84ead256-a292-4554-a51e-79f7b5f08f91/c4e4a645-ac53-4b38-a3d5-7237724bb338/flows#55e84220-3724-47c5-b180-b1c57c8fc143),
[filtering](https://mobbin.com/apps/trulia-ios-84ead256-a292-4554-a51e-79f7b5f08f91/c4e4a645-ac53-4b38-a3d5-7237724bb338/flows#aa63514b-ca78-4217-99ff-52fbc54ac4fb),
and [detail](https://mobbin.com/apps/trulia-ios-84ead256-a292-4554-a51e-79f7b5f08f91/c4e4a645-ac53-4b38-a3d5-7237724bb338/flows#3ef0a177-0c24-4d93-b241-6582c00386ce)
references are follow-up targets; their ordered transitions were not inspected.
Capture dates and exact versions remain unverified for all three apps.

Prioritize Zillow and Redfin for housing discovery composition, retain Glassdoor for card
hierarchy and inline provenance, and use Trulia for further transition research when accessible.
On/Turo remain secondary filtering references. Compass and Opendoor are additional housing
candidates, while Vrbo concerns vacation stays and Houzz concerns remodeling; their catalogs
were found, but their screens and flows were not inspected in this pass.

These observations support keeping familiar location/filter/result/detail stages. They do not
establish Rentora's globe entry, municipality-only map, status history, or modal usability.
All inspected housing screens are native iOS references; validate the responsive browser PWA
separately rather than importing native bottom navigation or gestures.

### Filter follow-up and map alignment

Follow-up date: 2026-11-15.

The authenticated [Turo filtering flow](https://mobbin.com/flows/4e15f54c-7a7f-4ac5-a54d-769a251f4b8f)
exposed seven ordered screens. Two readable frames were inspected: screen 6 shows a dedicated
filter surface with Reset, a selected-filter count, and a prominent "Show 200+ results" action;
screen 7 shows the returned listing with the selected-filter badge still visible. A cropped
picker frame also showed a Done control, but did not establish picker opening/dismissal
behavior. Another capture failed. The complete interactive flow was not replayed, and exact
capture date/app version remain unverified.

The public On/Turo descriptions were rechecked and still describe choosing attributes before
returning to filtered listings. Trulia's filtering catalog failed to reopen; no additional
Trulia transition evidence was obtained. These limits do not justify inventing competitor
behavior or treating native-app screenshots as PWA accessibility evidence.

The Rentora prototype's transaction/property fields opened separate centered choice overlays.
For its small, fixed option sets, these added context changes without a useful navigation step.
The maintainer selected one mobile filter bottom sheet with inline choices and fixed Apply/Reset
controls. This is a Rentora design decision informed by the references and the reported
confusion. Reset-to-all, draft preservation, and immediate municipality changes remain the
maintainer-confirmed rules; the Turo captures do not establish those semantics.

The [Politicometro city map](https://www.politicometro.org/br?uf=sc&cidade=4205407) was rechecked.
Once loaded, its breadcrumb showed Brazil, Sul, Santa Catarina, and Florianopolis; the map
exposed named state/municipality regions and a labeled city-search alternative. Rentora's
geographic narrowing and visible location context follow the same general intent, adapted
to its official RMF-only service area.

The current Rentora Figma prototype has a labeled map placeholder and two municipality choices.
It does not demonstrate globe navigation, geographic drill-down, or map-to-results return
behavior. Those remain a map-focused review requirement before final flow approval. Continue
using clearly labeled conceptual states until municipal geometry rights are verified;
do not invent shapes or copy Politicometro's assets, data, or implementation.

### Validating the adapted Rentora flow

Before describing the Rentora journey as validated:

1. Follow the maintainer-confirmed Apply, filter-only Reset, and preserved detail-return rules
   in the [frontend brief](./frontend-brief.md#interaction-and-layout). Confirm municipality-change
   behavior before implementation. Competitor screenshots do not settle these decisions.
2. After visual approval, test a fixture-backed prototype with target renters/buyers using
   realistic tasks: choose a municipality and property type, refine the results, inspect
   sourced facts and dates, then continue to the original source.
3. Include no matches, unavailable approved data, missing/stale facts, and returning from
   details. Check whether participants distinguish these states without prompting.
4. Record completion without assistance, errors, time on task, and understanding of source
   and date labels. Agree on pass criteria before sessions; report participant/device context
   and observed results without claiming population-wide validation from a small pilot.
5. Verify keyboard and screen-reader completion, modal focus, and mobile/desktop layouts.
   Automated tests check behavior; they do not establish usability by themselves.

No participant test was run during this reference research. A subsequent
[first Figma wireframe pass](./frontend-brief.md#first-figma-wireframes) records the structure;
it has no clickable connections and is not usability-validation evidence.

### Recommendations for the approved brief

- Keep the approved municipality/filter/list/detail journey. Public references show related
  discovery structures; they do not require a new Rentora navigation model.
- Use the Airbnb example to evaluate the relationship between map context, filtering, and item
  preview, not as a visual template or property-location model.
- Use On and Turo as bounded filter-to-results references. The public descriptions do not
  establish apply/reset behavior or state preservation; use the subsequently confirmed
  Rentora rules. Request behavior, focus announcements and state restoration still need tests.
- Keep source attribution and observation dates as Rentora-specific facts beside card and
  detail content. Glassdoor's visible compensation-source labels support studying inline
  provenance, but its relative-age labels do not prove Rentora's status-history semantics.
- Use Glassdoor's repeated card hierarchy as a comparison reference, not its personalized
  home experience. The detail preview is a full screen; Rentora retains the approved modal.
- Use Zillow and Redfin as primary housing composition references. Their map/result context
  supports studying visible criteria and result counts, not adopting pins or a draggable drawer.
- Do not infer that competitors' screenshots meet WCAG 2.2 AA. Rentora still needs keyboard,
  screen-reader, contrast, and responsive checks.

**Decision**: Add these links as research inputs to the frontend brief. Do not change approved
scope or choose a visual identity from them. Screenshots, assets, and original copy were not
vendored into the repository.

**Follow-up**: With authorized access or user-supplied references, review Glassdoor's
remaining search/filter/detail transitions and a complete real-estate discovery flow, including empty
results and returning from details. Record canonical links, capture/version metadata when
available, visible behavior, and the remaining inferences. A usability check with Rentora's
target users must validate the adapted flow; Mobbin inclusion is not that validation.
