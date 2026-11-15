# Approved Source Ingestion Boundary

**Status**: Source-agnostic design gate. No candidate provider is approved.

## Approval Preconditions

Before creating or enabling an adapter for any source, record and review:

1. The authorized access method (for example, an explicitly permitted API, licensed feed, or
   written partner agreement).
2. The permitted fields, display/republication rights, attribution, and link behavior.
3. Rate limits, caching restrictions, freshness terms, and retention/deletion rules.
4. The owner responsible for rechecking changed terms and suspending access.

Public visibility, an API endpoint, a sitemap, or `robots.txt` does not by itself establish
permission to collect or republish data. Do not scrape, automate around authentication, bypass
rate limits, or evade access controls.

## Adapter Output

An approved adapter may map permitted source records into the canonical model:

- stable source identifier and original listing link, when permitted;
- rent/sale and house/apartment classification when supported;
- municipality and optional neighborhood when verified;
- allowed factual fields with source field and observation timestamp;
- source-reported availability and status observation time, when available;
- required attribution and data freshness metadata.

The adapter must not invent missing fields, calculate an overall property score, or emit exact
property addresses/coordinates into the public model.

## Failure Handling

- Permission state other than `approved` disables collection and publication.
- Rate limiting and source outages are surfaced in operational logs and do not produce success
  shaped empty data.
- Invalid or contradictory source records are quarantined for review, not silently normalized.
- Retention and deletion follow the approved source policy.

Provider-specific authentication, endpoint details, and schemas are intentionally omitted until
a source passes the approval gate.
