# Rentora

<!-- impeccable:product-schema 1 -->

## Platform

web

## Stack

The confirmed preferred stack is a pnpm monorepo with a React 19+ PWA and a NestJS API.
The [implementation plan](./specs/001-aggregate-property-listings/plan.md) owns dependency,
package-boundary, Docker, and deployment decisions. These are preferences to verify at
implementation time, not existing application capabilities.

The maintainer selected shadcn/ui with Tailwind as the UI component foundation on 2026-11-15.
Components are not installed yet. This choice does not approve shadcn's default theme or a
Rentora visual identity; the plan owns component sourcing, compatibility, and maintenance.

## Users

Prospective renters and buyers considering a house or apartment in the officially defined
Florianopolis metropolitan region. They browse as guests and need to understand available
property facts, where those facts came from, and when a status was observed.

## Product Purpose

Help visitors discover relevant properties and review sourced information before continuing
to the original listing. The [feature spec](./specs/001-aggregate-property-listings/spec.md)
owns the acceptance criteria and Phase 1 scope.

## Operating Context

Visitors enter a globe-style map with Brazil and Florianopolis as the default market, choose
an in-scope municipality, filter rent/sale and house/apartment, and optionally filter by a
source-supported neighborhood. Results appear in one factual card list. Selecting a card
opens a details modal with facts, source links, and available dated status observations.

Local implementation validation must support Docker Compose for web, API, and PostgreSQL.
The repository currently has planning documents and agent tooling, but no runnable app.

## Capabilities and Constraints

- Phase 1 is read-only and covers the official metropolitan region only.
- No accounts, comments, reviews, user submissions, ratings, rankings, overall scores,
  status alerts, property pins, exact addresses, or property coordinates.
- Preserve source attribution, observation dates, uncertainty, and missing-data states.
  A prior observation must not imply that a property is currently available.
- External data requires verified collection and reuse permission. No candidate listing
  provider is approved. Municipal boundary licensing and production map-provider selection
  remain unresolved.
- Synthetic fixtures are for clearly labeled local or controlled validation only.
- The initial interface language is Brazilian Portuguese (`pt-BR`). Other translations and a
  visible language switcher are outside the current implementation scope.
- Prepare for future locale switching: separate translatable UI copy from components,
  use stable message keys and locale-aware formatting, and preserve interpolation
  placeholders. A future locale should not require rewriting component markup.
- Do not automatically translate source-reported property facts or imply that a translation
  has been verified. A localization library remains an implementation-plan decision.

## Brand Commitments

The product name is Rentora. UI prose should be plain, factual, and clear about uncertainty.
Politicometro is the user's interaction reference for map-to-location navigation; its assets,
colors, and typography are not an approved Rentora visual identity.
On 2026-11-15, the maintainer selected "Consulta clara" and confirmed the representative Figma
design brief. Its direction uses a deep-blue shell, light-blue context panels, white reading
surfaces, workhorse sans-serif UI text, and compact factual summaries with a consistent
source/date line. The [frontend brief](./specs/001-aggregate-property-listings/frontend-brief.md)
records the selected palette roles and remaining review steps. Final responsive composition,
control/state specifications, and runtime font decisions remain pending. No logo or imagery
assets have been approved.

## Evidence on Hand

- [Constitution](./.specify/memory/constitution.md): engineering and source-permission rules.
- [Feature spec](./specs/001-aggregate-property-listings/spec.md): confirmed user flows.
- [Research](./specs/001-aggregate-property-listings/research.md): source and map evaluations.
- [Public Mobbin research](./specs/001-aggregate-property-listings/research.md#public-mobbin-flow-research):
  inspected Airbnb, Glassdoor, Zillow, and Redfin previews, Trulia catalog metadata, and published
  On/Turo filter-flow descriptions. These are inspiration, not Rentora usability-validation
  evidence; full discovery transitions remain unverified.
- [First Figma wireframes](./specs/001-aggregate-property-listings/frontend-brief.md#first-figma-wireframes):
  editable structural drafts the maintainer considers a good starting point. They use synthetic
  listings and a map placeholder, have no clickable connections, and have not yet been styled
  with the selected direction. Detailed screen review remains pending.
- No implemented UI, licensed listing feed, or approved logo/imagery assets exist yet.
  Do not invent property facts, testimonials, endorsements, or success metrics.

## Product Principles

- Make source and date information easy to find beside the facts it qualifies.
- Help visitors narrow a location and understand a listing without signing in.
- Represent unavailable information honestly.
- Preserve the Phase 1 privacy and source-permission boundaries.

## Accessibility & Inclusion

The confirmed accessibility target is WCAG 2.2 AA.
Municipality selection must have a keyboard-accessible alternative to pointing at the map.
Filters and detail modals must support keyboard use, visible focus, and assistive technology.
The approved [frontend brief](./specs/001-aggregate-property-listings/frontend-brief.md) owns
surface layout, states, and accessibility implementation guidance.
