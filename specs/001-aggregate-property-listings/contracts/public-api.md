# Proposed Public API Contract

**Status**: Design contract only. Routes and JSON shapes are proposals for the later API design;
no endpoint is implemented.

## Contract Principles

- The API is read-only and requires no user account for Phase 1.
- Only data from approved sources may be returned.
- All listed details carry source attribution and observation/freshness dates when known.
- Do not return exact addresses, coordinates, overall scores, ratings, or rankings.
- Missing values remain absent or explicitly unknown; never fabricate them.
- List responses are paginated and filterable. Empty results return an empty collection, not an
  error.

## `GET /api/v1/areas`

Returns selectable geographic areas for the globe-to-municipality flow.

Query:

- `parentId` (optional): return children of a country, state, or municipality.

Response shape:

```json
{
  "defaultCountryId": "BR",
  "defaultMunicipalityId": "4205407",
  "items": [
    {
      "id": "4205407",
      "name": "Florianópolis",
      "level": "municipality",
      "parentId": "SC",
      "selectable": true,
      "regionClass": "core"
    }
  ]
}
```

The sample municipality ID is the official IBGE code observed for Florianópolis; confirm all
codes against the boundary dataset before implementation. Only Phase 1 RMF municipalities are
selectable. Geometry is delivered only from a separately licensed boundary asset.

## `GET /api/v1/listings`

Returns one flat list of property cards.

Required query parameters:

- `municipalityId`

Optional query parameters:

- `transactionType`: `rent` or `sale`; omission includes both transaction types.
- `propertyType`: `house` or `apartment`; omission includes both property types.
- `neighborhoodId`: accepted only when authorized data supports that neighborhood.
- `cursor`, `limit`: pagination.

Reset clears the client-side type and neighborhood drafts but retains the municipality.
After Apply, cleared filters are omitted from the query; `municipalityId` remains required.
Empty strings and unknown type values are invalid, not equivalent to omission.
Changing municipality sends a query with the applied transaction/property filters and no
neighborhood filter. Unapplied transaction/property edits are not sent by that change.

Each card includes an internal listing ID, source-supported municipality/neighborhood, factual
highlights, source name, original source link when available and permitted, current source status
when verified, and known observation time. No exact location or overall score is returned.

## `GET /api/v1/listings/{listingId}`

Returns the detail-modal data for one listing:

- factual fields and their source/verification status;
- municipality and neighborhood, if supported;
- current source-reported status and retained status observations with dates;
- source attribution, original listing link, and last-known verification time.

The response must omit exact address and coordinates even if a source record contains them.
Status history does not trigger alerts.

## Failure and Empty States

- No matches: `200` with `items: []` and pagination metadata.
- Unknown listing: `404`.
- Municipality outside Phase 1: `400` with a stable `AREA_NOT_IN_PHASE1_SCOPE` code.
- Source temporarily unavailable: serve only permitted stored data with its true freshness, or
  return an explicit service error. Do not present stale data as current.

Authentication, write operations, user profiles, comments, ratings, and property submissions are
not part of this contract.
