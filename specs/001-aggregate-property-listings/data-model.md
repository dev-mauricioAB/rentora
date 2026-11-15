# Data Model: Aggregate Property Listings

**Status**: Proposed; source-specific fields must be validated against an authorized source before
implementation.

## Design Rules

- Keep domain types independent of NestJS, React, Prisma, and the selected map provider.
- Preserve source attribution and observation time for every displayed fact.
- Do not infer missing property details, status, municipality, or neighborhood.
- Do not expose exact addresses or coordinates. Do not create an overall score or rating.
- A source cannot create or update listings until its access, reuse, attribution, and retention
  permissions are recorded as approved.
- Treat one source record as one listing in Phase 1; do not merge cross-source duplicates until
  there is a verified matching rule.

## Entities

### Service Region

Represents the Phase 1 service boundary and its policy-defined membership.

| Field | Meaning |
|---|---|
| `id` | Stable internal identifier for the RMF scope |
| `name` | Região Metropolitana de Florianópolis |
| `legalReference` | Santa Catarina LC 495/2010, Article 5, plus checked amendments |
| `membershipReviewedAt` | Date the official municipality list was last reviewed |

The current list contains nine core and thirteen expansion-area municipalities. See
[research.md](./research.md) for the municipality names and official references.

### Administrative Area

Represents a selectable municipality in the service region.

| Field | Meaning |
|---|---|
| `id` | Stable municipality identifier; prefer the official IBGE code after verification |
| `name` | Official municipality name |
| `parentId` | Parent state or country in the map hierarchy |
| `serviceRegionId` | RMF membership |
| `regionClass` | Core or expansion area |
| `selectable` | Whether the municipality can be selected in Phase 1 |
| `boundaryAssetRef` | Reference to separately sourced and licensed boundary geometry |

The map uses municipal areas, not property-point coordinates. Boundary geometry must not be
bundled until its license and attribution requirements are verified.

### Neighborhood

Optional source-supported area label associated with an administrative area.

| Field | Meaning |
|---|---|
| `id` | Internal identifier scoped to a municipality |
| `municipalityId` | Parent municipality |
| `name` | Neighborhood name as supplied or verified |
| `sourceId` | Source of the label or verification |
| `verifiedAt` | Last verification time, when known |

Neighborhoods are shown as filter options only when supported by authorized, reliable data. An
exact address must not be used to infer a neighborhood.

### Listing Source

Represents a data provider and its authorization state.

| Field | Meaning |
|---|---|
| `id` | Internal source identifier |
| `name` | Human-readable source name |
| `permissionState` | `unverified`, `pending`, `approved`, or `suspended` |
| `permissionEvidenceRef` | Reference to the applicable agreement, terms, or approval record |
| `allowedFields` | Fields permitted for collection and display |
| `retentionPolicy` | Permitted retention duration and deletion conditions |
| `attributionText` | Required user-visible attribution |
| `sourceTermsUrl` | Applicable public terms, when available |
| `reviewedAt` | Date access and reuse permission was reviewed |

Only `approved` sources may supply data to the public listing experience. A source must be
suspended if permission expires or becomes uncertain.

### Property Listing

One property listing record from one approved source.

| Field | Meaning |
|---|---|
| `id` | Internal listing identifier |
| `sourceId` | Approved listing source |
| `sourceRecordId` | Source record identity, if permitted and stable |
| `sourceUrl` | Original listing link, if permitted and available |
| `transactionType` | `rent` or `sale` |
| `propertyType` | `house` or `apartment` |
| `municipalityId` | Verified municipality association |
| `neighborhoodId` | Optional verified neighborhood association |
| `facts` | Source-attributed factual details permitted for display |
| `sourceStatus` | Original status value supplied by the source, if available |
| `currentStatus` | Normalized status only when source semantics support it |
| `lastObservedAt` | Most recent observation time |
| `lastVerifiedAt` | Most recent verification time, when distinct and known |

Do not persist exact coordinates or addresses for Phase 1. A listing must not be shown if its
municipality cannot be verified or if its source is not approved.

### Listing Fact

An optional factual value associated with a listing.

| Field | Meaning |
|---|---|
| `name` | Canonical display key, if safely mapped |
| `value` | Value permitted by the source terms |
| `unit` | Unit for numeric values, when applicable |
| `sourceField` | Corresponding field or fact in the source |
| `observedAt` | Time the value was observed |
| `verificationState` | `source_reported`, `verified`, or `unknown` |

Only available, permitted facts appear. Do not manufacture a rating, inferred condition, or
composite score.

### Availability Observation

An append-only record of an availability/status observation.

| Field | Meaning |
|---|---|
| `id` | Internal observation identifier |
| `listingId` | Associated listing |
| `sourceStatus` | Original status text/value supplied by the source |
| `normalizedStatus` | Optional normalized value, only if the mapping is verified |
| `observedAt` | Time Rentora observed the status |
| `sourceUpdatedAt` | Source-provided update time, if available |
| `sourceUrl` | Source reference for this observation, if permitted |

The UI labels observations with their known dates. Phase 1 has no alerts. Retention must follow
each source's approved policy; do not retain records beyond that policy.

## Relationships and Lifecycle

- One Service Region contains many Administrative Areas; Phase 1 selects only RMF municipalities.
- One Listing Source supplies many Property Listings; each listing has exactly one source.
- One Property Listing belongs to one verified municipality and may have one verified
  neighborhood.
- A listing has zero or more Listing Facts and Availability Observations.
- A source transitions from `unverified` → `pending` → `approved`; it may transition to
  `suspended` if permission or access becomes invalid.
- A listing is shown only while its source is approved and the listing is within the service
  area. Unknown or stale status is shown as unknown/stale, not as available.
- Cross-source deduplication, user identity, comments, reviews, and notifications are outside
  this feature.
