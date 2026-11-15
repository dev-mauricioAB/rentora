# Feature Specification: Aggregate Property Listings

**Feature Branch**: `001-aggregate-property-listings`

**Created**: 2026-11-15

**Status**: Draft

**Input**: User description: "Phase 1 should centralize public information about homes and apartments that users may want to rent or buy, initially focused on Florianópolis. Potential sources mentioned are Google, Airbnb, public Facebook groups, and QuintoAndar. The first phase is intended for reading information only; accounts and comments may come in later phases."

## Clarifications

### Session 2026-11-15

- Q: What should the main property score represent in Phase 1? → A: No overall score; show sourced factual highlights.
- Q: Should the Phase 1 listing modal show an exact property location, or only the municipality/neighborhood and source link? → A: Show municipality/neighborhood only; do not show exact property locations in Phase 1.
- Q: How should Phase 1 organize the property results after a municipality is selected? → A: One filterable list of factual property cards.
- Q: When should filter edits update results? → A: After the visitor presses Apply; municipality selection is an immediate location change.
- Q: What should Reset clear? → A: Transaction, property-type, and neighborhood filters; keep the selected municipality. Applying the cleared filters shows all supported transaction and property types there.
- Q: Should closing details preserve filters and scroll position? → A: Yes; return to the same result position.
- Q: What happens when the municipality changes? → A: Refresh with applied transaction/property filters and clear both applied and draft neighborhood selections.
- Q: What happens to unapplied transaction/property edits when the municipality changes? → A: Preserve those draft edits; they still require Apply.
- Q: How should details fit smaller screens? → A: An adaptive bottom-sheet modal on mobile, sized to content and growing nearly full-screen while leaving dimmed discovery context visible when space allows. Use full-screen only when reading space requires it. Keep a fixed title/close header and scrollable content; desktop uses a centered, height-limited modal.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Discover properties by type and area (Priority: P1)

A prospective renter or buyer visits the site without signing in, chooses whether they want to
rent or buy and whether they want a house or apartment, starts from a globe-style map with Brazil
and Florianópolis as the default market, selects a municipality in the Região Metropolitana de
Florianópolis, optionally filters by neighborhood, and reviews matching property listings.

**Why this priority**: Finding relevant properties is the central value of the first release.

**Independent Test**: With a prepared set of in-scope listings, a visitor can start from the
default Brazil/Florianópolis market, select a municipality in the metropolitan region, choose a
transaction and property type, optionally filter by a neighborhood with available listing data,
see matching factual property cards in one list, and understand when there are no matches.

**Acceptance Scenarios**:

1. **Given** a visitor who is not signed in, **When** they open the map, **Then** Brazil and
   Florianópolis are the default market.
2. **Given** no listings match the selected criteria, **When** the visitor searches, **Then** the
   site clearly communicates that no matching listings were found.
3. **Given** a visitor selects a municipality within the Região Metropolitana de Florianópolis,
   **When** they choose rent or buy and house or apartment, **Then** they see only matching
   listings for that municipality.
4. **Given** a visitor attempts to select a municipality outside the Phase 1 service area,
   **When** they use the map, **Then** that municipality is unavailable for selection.
5. **Given** neighborhood information is available for listings in a selected municipality,
   **When** a visitor chooses a neighborhood filter, **Then** only listings from that
   neighborhood are shown.
6. **Given** applied filters and different draft edits, **When** a visitor changes municipality,
   **Then** results refresh using the applied transaction/property filters, both neighborhood
   selections clear, and the draft transaction/property edits remain unapplied.
7. **Given** applied filters, **When** a visitor resets them, **Then** the municipality and
   current results remain unchanged until Apply; applying shows all supported transaction
   and property types in that municipality.
8. **Given** draft transaction/property edits, **When** the visitor has not pressed Apply,
   **Then** those edits do not change results or trigger a listing request.

---

### User Story 2 - Review factual property details (Priority: P2)

A visitor selects a listing item to open a details modal with its available factual information
and external source links. They can distinguish information supplied by its source from
information that has not been verified. Where status observations exist, the visitor can review
their history; Phase 1 does not send change alerts or show an overall property score.

**Why this priority**: Users need enough context to assess a property while avoiding the false
impression that aggregated details are guaranteed to be current.

**Independent Test**: Select a listing with known source data and verify the details modal shows
available facts with their source, only municipality/neighborhood-level location, no exact
property location, and no overall score.

**Acceptance Scenarios**:

1. **Given** a listing with source and verification information, **When** a visitor reviews it,
   **Then** the source and known verification time are visible.
2. **Given** a visitor selects a listing item, **When** its details are available, **Then** a
   modal opens with the listing's available facts and external source links.
3. **Given** a listing with missing or unverified information, **When** a visitor reviews it,
   **Then** the site does not present that information as confirmed.
4. **Given** a listing with multiple recorded status observations, **When** a visitor reviews
   it, **Then** the observations and their known dates are distinguishable from its current
   source-reported status.
5. **Given** a listing with factual details, **When** a visitor sees its summary or details,
   **Then** no overall score, rating, or ranking is presented.
6. **Given** a listing with exact address or coordinates in its source data, **When** a visitor
   views its summary or details, **Then** the app shows no exact property location.
7. **Given** details exceed the available viewport height, **When** a visitor scrolls the modal,
   **Then** all detail content remains reachable and the title/close header remains available.
8. **Given** an open detail modal, **When** the visitor closes it, **Then** the same applied
   filters and result scroll position remain, and focus returns to the invoking control.
9. **Given** a mobile viewport with sufficient reading space, **When** details open, **Then**
   a bottom-anchored modal fits its content up to the available height, with dimmed discovery
   context visible behind it.
10. **Given** a short viewport or enlarged text leaves insufficient reading space in the sheet,
    **When** details open, **Then** the modal may occupy the full viewport while keeping its
    title/close header and all content reachable.

---

### User Story 3 - Continue to the original listing (Priority: P2)

A visitor can open the original source for a listing to review the source's full information or
continue their inquiry there.

**Why this priority**: The first release centralizes discovery without replacing the source
platform or introducing user-submitted content.

**Independent Test**: For every displayed listing, a visitor can follow its clearly identified
source link without creating an account on this site.

**Acceptance Scenarios**:

1. **Given** a displayed listing with an available original link, **When** a visitor selects it,
   **Then** the original source opens.
2. **Given** an original link is unavailable or no longer valid, **When** a visitor selects the
   listing, **Then** the site does not imply the source can still be reached.

### Edge Cases

- A listing is removed or changes after it was last verified.
- A source provides incomplete, contradictory, or outdated details.
- A source is unavailable or does not permit the proposed access or republication method.
- No listings match a user's selected property type, transaction type, or service area.
- A listing has no usable original source link.
- A municipality changes while unapplied edits or a neighborhood selection exist.
- Detail content exceeds the available viewport height.
- A short mobile viewport or enlarged text requires a full-height detail modal.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: Visitors MUST be able to browse listings without creating an account or signing in.
- **FR-002**: Visitors MUST be able to distinguish listings for rent from listings for sale.
- **FR-003**: Visitors MUST be able to distinguish houses from apartments.
- **FR-004**: Each displayed listing MUST identify its source and provide a link to the original
  listing when that link is available.
- **FR-005**: The site MUST distinguish source-provided or verified details from unavailable or
  unverified details.
- **FR-006**: The site MUST retain and display available status history with known observation
  dates. Phase 1 MUST NOT send users alerts about status changes.
- **FR-007**: A candidate source MUST remain excluded from collection and presentation until its
  permitted access and reuse methods have been verified and documented. Selection of specific
  sources is deferred until that verification is complete.
- **FR-008**: The initial service area MUST include the municipalities in the officially defined
  Região Metropolitana de Florianópolis.
- **FR-009**: The site MUST NOT display information obtained by bypassing access controls or
  without an approved basis and method for its collection and presentation.
- **FR-010**: Phase 1 MUST NOT require accounts, comments, reviews, or user-submitted listings;
  these are outside this release.
- **FR-011**: The map MUST allow visitors to select a municipality in the service area and filter
  the displayed listings to that municipality. The globe-style map MUST default to Brazil and
  Florianópolis; municipalities outside the official Região Metropolitana de Florianópolis MUST
  be unavailable in Phase 1. Phase 1 MUST NOT display individual property markers or exact
  property locations.
- **FR-012**: Selecting a listing item MUST open a details modal showing its available factual
  details and external source links.
- **FR-013**: Phase 1 MUST NOT present an overall property score, rating, or ranking. Listing
  summaries MUST use factual details with source attribution.
- **FR-014**: Listing summaries and detail modals MAY show a source-supported municipality or
  neighborhood, but MUST NOT disclose an exact property address or coordinates.
- **FR-015**: Visitors MUST be able to filter listings by neighborhood when authorized listing
  data includes neighborhood information. The site MUST NOT infer or fabricate a neighborhood.
- **FR-016**: Matching property listings MUST be presented in one filterable list of factual
  property cards, not grouped into separate neighborhood or property-type sections.
- **FR-017**: The initial interface MUST use Brazilian Portuguese (`pt-BR`). UI copy and
  locale-sensitive formatting MUST allow future language switching without restructuring
  components. Additional translations and a visible language switcher are deferred.
- **FR-018**: The interface MUST meet WCAG 2.2 AA, including keyboard-accessible municipality
  selection equivalent to the map, accessible filters, and accessible detail-modal navigation.
- **FR-019**: Transaction, property-type, and neighborhood edits MUST remain drafts until Apply.
  Reset MUST clear those draft filters while retaining the municipality and current results;
  applying cleared filters MUST include all supported transaction and property types there.
- **FR-020**: Changing municipality MUST refresh results using applied transaction/property
  filters, clear applied and draft neighborhood selections, and preserve unapplied
  transaction/property edits without applying them.
- **FR-021**: Mobile details MUST use an adaptive, bottom-anchored modal sized to content up to
  the available height, with dimmed discovery context visible when space allows. It MAY occupy
  the full viewport when a short viewport or enlarged text requires more reading space.
  Desktop details MUST use a centered, height-limited modal. Long detail content MUST scroll
  internally while the title/close header remains available. Closing MUST preserve applied
  filters and result position and restore focus.

### Key Entities *(include if feature involves data)*

- **Property Listing**: Information about a home or apartment offered for rent or sale, including
  its transaction type, property type, source-supported municipality or neighborhood, available
  details, source, and source link.
- **Listing Source**: The origin of listing information and the applicable attribution and
  verification details.
- **Availability Information**: Current and prior availability observations supplied by a source,
  when available, with their known observation dates.
- **Service Area**: The officially defined municipalities in the Região Metropolitana de
  Florianópolis that visitors can select on the map.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: 100% of displayed listings identify their source and provide an original listing
  link when one is available.
- **SC-002**: All four combinations of rent/buy and house/apartment return only matching listings
  in acceptance tests, and an unmatched search shows a clear empty result.
- **SC-003**: A visitor can complete the primary discovery flow and open a source listing without
  signing in to this site.
- **SC-004**: No listing from a source lacking a verified permitted access and presentation
  method is displayed.
- **SC-005**: 100% of retained status observations are distinguishable from the current status
  and show their source and known observation date.
- **SC-006**: Selecting any in-scope municipality on the map filters the results to listings
  associated with that municipality, with no individual property locations shown.
- **SC-007**: On entry, the map presents Brazil and Florianópolis as the default market, and
  users cannot select locations outside the Phase 1 service area.
- **SC-008**: Every listing details modal presents available source-attributed facts and external
  source links, and no listing summary or modal displays an overall score, rating, or ranking.
- **SC-009**: No listing summary or detail modal exposes an exact property address or coordinates.
- **SC-010**: Neighborhood filters show only neighborhoods supported by authorized listing data,
  and selecting one returns only listings associated with it.
- **SC-011**: Matching listings appear in one result list, and each card exposes factual
  source-attributed highlights without an overall score.
- **SC-012**: All initial interface messages use `pt-BR`. Localization validation confirms that
  UI messages and date/number formatting can use another locale without changing the component
  structure; no additional language or visible switcher is required for Phase 1.
- **SC-013**: A visitor can complete municipality selection, filtering, detail-modal review,
  and source-link navigation with a keyboard. Accessibility review of the discovery flow meets
  WCAG 2.2 AA through automated checks and manual keyboard/screen-reader validation.
- **SC-014**: Filter-state acceptance tests confirm that draft edits trigger no listing request,
  municipality changes use applied filters while clearing both neighborhood selections and
  retaining other drafts, and Reset requires Apply before unrestricted type results appear.

## Assumptions

- Phase 1 is a read-only discovery experience for prospective renters and buyers.
- The initial property types are houses and apartments; the transaction types are rent and buy.
- The initial area is the Região Metropolitana de Florianópolis as defined in the current official
  municipal list. Planning has verified the list in Article 5 of Santa Catarina Complementary Law
  495/2010 against the amendments reviewed through Complementary Law 860/2024.
- Google, Airbnb, public Facebook groups, and QuintoAndar are candidate sources, not approved
  integrations. Their terms and permitted access methods must be verified before use.
- Accounts, comments, reviews, and user-submitted listings are future-phase possibilities, not
  requirements for this feature.
- Status history is in scope, but status-change alerts are not.
- Property details open in a modal. Phase 1 shows source-attributed factual highlights and no
  overall score, rating, or ranking.
- Property location is limited to municipality or neighborhood in Phase 1; exact addresses and
  coordinates are not displayed.
- Neighborhood filters are available only when an authorized source provides reliable
  neighborhood data; the app does not infer a neighborhood from an exact address.
- Property results use one filterable list rather than separate groups.
- Map-based browsing is a core flow. The Phase 1 map selects municipalities; it does not display
  individual property pins or exact property locations. It opens with a globe-style view, with
  Brazil and Florianópolis as the default market; later phases may enable other cities or regions.
- Politicômetro is a user-provided interaction reference for the globe-to-location flow; its
  assets and implementation are not requirements for Rentora.
- The specific frameworks, libraries, hosting providers, and database services remain preferred
  architecture choices to evaluate during planning, not requirements of this user-facing spec.
