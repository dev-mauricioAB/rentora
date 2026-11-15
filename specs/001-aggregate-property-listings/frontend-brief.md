# Frontend implementation brief

Approved behavior and accessibility baseline: 2026-11-15.
Selected visual direction and representative Figma brief: Consulta clara, 2026-11-15.
Final responsive composition and component/state specifications: not yet approved.

## Job and outcome

Help a guest renter or buyer narrow a municipality and property type, understand sourced facts
and their dates, then continue to the original listing. This is an Impeccable Operate surface.
Use [PRODUCT.md](../../PRODUCT.md) for audience and language context and
[spec.md](./spec.md) for product requirements. This brief does not override either document.

The maintainer confirmed mixed phone and computer use with neither clearly dominant as the
working usage assumption. This is not participant research. No additional brand restrictions
or existing assets were supplied for this direction round.

## Interaction and layout

- On mobile, use a vertical flow: location/map, filters, then one result list.
- On desktop, keep location/filter context beside that same result list. Do not split results
  into multiple neighborhood or property-type groups.
- Preserve the globe-style entry and Brazil/Florianopolis defaults. Only in-scope municipalities
  are selectable. Provide an equivalent labeled keyboard-accessible municipality control.
- Make selected filters and municipality clear. A neighborhood filter is available only when
  authorized data supports it; do not infer neighborhoods.
- Keep transaction/property/neighborhood edits separate from applied criteria. Those edits
  update results only after Apply; do not issue listing requests for each draft filter change.
- Mobile filters use one bottom sheet with visible transaction/property choice groups and
  fixed Apply/Reset controls. Selecting a type updates its draft in place, without opening
  another menu. Keep the selected value identifiable with text and a selected outline.
  Municipality selection may use its separate selector. Desktop retains its context sidebar.
- Municipality selection is immediate: refresh with applied transaction/property filters,
  clear both applied and draft neighborhood selections, and preserve draft transaction/property
  edits without applying them. This rule applies even if a neighborhood name exists in both
  municipalities.
- Reset clears draft transaction, property-type, and neighborhood values while keeping the
  municipality and current results. Apply is still required; cleared types mean all supported
  transaction/property types. The [API contract](./contracts/public-api.md) represents these
  cleared filters by omitted optional query parameters.
- Use a mobile bottom-sheet detail modal with rounded top corners and dimmed discovery context
  behind it. Fit its height to content, growing nearly full-screen with a visible context strip
  when space allows. A short viewport or enlarged text may require full-screen reading space.
  Keep the title/close header fixed and scroll long content internally. Desktop retains a
  centered, height-limited modal with the same fixed-header/internal-scroll behavior.
  Final composition still requires maintainer review.
- A card opens a details modal. Provide a distinct original-source link when available;
  avoid nested interactive controls and ambiguous click targets.
- Closing details preserves applied filters and list scroll position, returning to the invoking
  card/control.
- Keep source and known observation/verification dates visible with the facts they qualify.
  Distinguish past status observations from current source-reported status.

## Required states

| State | Required behavior |
|---|---|
| Loading | Identify pending content accessibly; do not show invented listing content |
| No matches | Explain the empty result and keep filter/location controls available |
| No approved data | Explain that listing data is unavailable; do not imply an outage or no market supply |
| Missing fact | Identify unavailable information without filling it with a guess |
| Stale status | Show the true observation date and uncertainty; do not label it currently available |
| Request failure | Show an actionable failure state and retry where appropriate |
| Map failure | Keep the equivalent municipality control usable and report the map failure |
| Missing source link | Explain that the original link is unavailable; do not show a working-looking action |

## Accessibility acceptance

Target WCAG 2.2 AA. Validate against the standard, not just an automated score.

- All filters, municipality selection, cards, links, and modal controls support keyboard use
  with visible, unobscured focus and meaningful accessible names.
- A modal receives focus on opening, keeps focus within it, supports Escape and a labeled close
  control, and returns focus to the invoking card/control on closing.
- Status and map selection do not rely on color alone. Supply text labels.
- Meet AA contrast: at least 4.5:1 for normal text, 3:1 for large text, and applicable 3:1
  non-text contrast for controls and state indicators.
- Support text resizing and reflow without losing controls or content. The map's spatial layout
  does not excuse inaccessible filters or results.
- Announce important loading, result, and failure changes without moving focus unexpectedly.
  Respect reduced-motion preferences during globe and modal transitions.
- Review touch target sizing, screen-reader navigation, and keyboard behavior manually.

## Localization

Ship `pt-BR` initially. Use stable UI message keys, interpolation, and locale-aware date/number
formatting. Avoid assembling sentences from translated fragments or hard-coding UI prose across
components. A new message catalog should support another locale without restructuring the UI.
Do not implement additional translations or a visible language switcher now.
Keep source names, facts, units, and observation instants faithful to their provenance.

## UI component foundation

shadcn/ui with Tailwind was approved on 2026-11-15. The [plan](./plan.md) owns sourcing,
compatibility, presentation-layer boundaries, and update maintenance. No library components
are installed yet, and the neutral Figma wireframe buttons do not establish the implementation
library or final appearance.

The first visual review should cover the required buttons, labeled filters, municipality
selector, detail dialog, source links, loading indicators, and inline failure messages.
Use these alongside Rentora-specific factual cards, source/date information, and status
history. Add components only when the flow requires them.

Proposed button hierarchy for review: emphasize Apply in the filter area and the usable
original-source action in details; keep Reset lower emphasis and separate from Apply.
Reset is not a destructive action. Review hover, pressed, focus, disabled, and loading states
with the palette. These treatments remain proposals until visual approval.

## Visual planning sequence

1. Discovery completed: mixed phone/computer use, no additional brand restrictions. Preserve
   the approved flow, locale, and accessibility target; validate real use conditions later.
2. Direction selection completed through Impeccable new-work and concept-seed. The maintainer
   selected Consulta clara and confirmed the representative design brief. The local selection
   reference is `seed:904b7913eac7ceae`; the selected grounded candidate was first in the list.
3. A representative styled Figma slice is created and visually checked. Maintainer review is
   pending for the mobile/desktop results, filters, factual cards, details, and
   missing/stale/failure treatments. Compare palette roles, typography, spacing, control
   variants, and focus/contrast specifications together. See the review notes below.
4. The confirmed flow is connected in a separate clickable prototype. Municipality-change,
   draft preservation, Reset-to-all, and modal-presentation rules were confirmed on 2026-11-15.
   Browser checks and their limits are recorded below. Final screens and component states
   still require approval; prototype checks do not establish runtime accessibility.
5. Hand off the approved composition and component/state specifications to task planning.
   Keep UI implementation dependent on approval and use the validation section below once
   runnable. Scaffolding and non-UI task planning remain independent of visual approval.

The shape brief is confirmed. The representative Figma pass is ready for maintainer visual
review; final responsive and interaction specifications remain pending. UI code or a direction
contract must wait for approval. DESIGN.md must not claim a shipped system at this stage.

### Selected direction: Consulta clara

- Use the selected study's deep-blue shell (`#173A5E`), pale-blue context (`#E9F2F8`), white
  reading surfaces (`#FFFFFF`), and blue primary action (`#1F5F8B`). These are direction values,
  not a complete implemented token system. Semantic failure/warning colors and control states
  still need specifications and review; brand blue does not certify a property fact.
- Use workhorse sans-serif UI typography and compact, consistent spacing. The selected study
  uses a system sans-serif stack. The maintainer retained `system-ui` as the runtime font and
  Noto Sans as the Figma representation proxy on 2026-11-15. Resolve sizes and responsive
  behavior in the handoff rather than treating Inter or Noto Sans as a runtime requirement.
- Keep the approved desktop context beside one list and the mobile location/filter/list order.
  The signature is a consistently positioned source/date line on every factual summary,
  with explicit missing or unconfirmed information nearby. Opening details must preserve
  applied criteria and result position; the study does not implement that interaction.
- The maintainer confirmed the next review scope: mobile and desktop results, filters, one
  factual card, detail content, and missing/stale/error states. Review button hierarchy and
  focus/contrast specifications before extending the design to the full prototype.

The selected local study is a static first-viewport exploration with synthetic data and a
labeled map placeholder. It is not React/shadcn implementation, a clickable Figma prototype,
or evidence of usability or WCAG compliance. Desktop inspection found no horizontal control
clipping at the observed 1358px viewport, a minimum measured text contrast of 6.55:1, and
controls at least 44px high. Mobile reflow, full keyboard/modal behavior, and screen-reader
validation remain unverified.

## Before the first UI code

1. Read the constitution, feature spec, plan, PRODUCT.md, this brief, and frontend instructions.
2. Inherit Consulta clara and use the relevant Impeccable workflow to resolve and obtain approval
   for the final responsive composition and component/state specifications. Do not reopen the
   direction round unless the maintainer requests a change, or quietly choose logos, imagery,
   or map styling.
3. Record the approved visual direction using that workflow. A future DESIGN.md must distinguish
   approved direction from tokens/components actually implemented; do not fabricate shipped UI.
4. Load the relevant playbook and craft floor immediately before UI edits. Build this app
   surface code-first; [.impeccable/config.json](../../.impeccable/config.json) also records the
   chosen code-first default for future landing/showcase surfaces.
5. Reuse UI components and shared styling when they exist; preserve domain/package boundaries.

Scaffolding and task planning may proceed before final composition approval. User-facing UI
implementation must wait for that approval. A direction is selected, but no runnable app or
complete implemented visual system exists yet.

## Discovery references

Read [public Mobbin flow research](./research.md#public-mobbin-flow-research) before proposing
the visual composition. The inspected Airbnb map/card screen, Glassdoor home/detail previews,
and On/Turo filter references provide related discovery patterns, not usability-validation
results or a Rentora visual identity. Zillow and Redfin's inspected map/results highlights are
the primary housing composition references; Redfin's detail summary is another factual-hierarchy
comparison. Trulia's named search/filter/detail flows remain follow-up targets with metadata-only
evidence. Full discovery transitions remain unverified because catalog access is limited; do not
infer filter/reset, drawer gestures, or return behavior from isolated screenshots. These native
iOS references do not establish the responsive PWA's behavior. Apply/reset and detail-return
behavior were subsequently confirmed with the maintainer and are recorded above.
The [filter follow-up](./research.md#filter-follow-up-and-map-alignment) adds readable Turo
filter/results frames; it does not establish the complete housing discovery flow.

Preserve this brief's approved behavior and scope. Do not import ratings, favorites, account
requirements, booking, alerts, or exact property pins from comparison apps. Apply/reset
and detail-return behavior follow the confirmed interaction rules, not competitor screenshots.
Municipality-change behavior follows the confirmed immediate-refresh and draft-preservation
rules above.

Use the [adapted-flow validation steps](./research.md#validating-the-adapted-rentora-flow) for a
fixture-backed usability check after visual approval. Agree on pass criteria before sessions and
record observed completion and understanding of provenance/dates. Reference research alone does
not validate Rentora's globe entry, municipality-only map, status history, or details modal.

### First Figma wireframes

[Rentora wireframes v1](https://www.figma.com/design/q4bHJWPJo1H4eBEZePP2fA/Rentora?node-id=3-130)
contains four mobile journey states, desktop results/detail layouts, and three failure or
unavailable-data state frames. These are editable, grayscale structural drafts using clearly
synthetic listings. Inter and neutral tones are temporary wireframe conventions, not approved
brand choices. The map is a labeled placeholder; no municipal geometry or source integration
is implemented.

The maintainer considers this first pass a good starting point. Detailed screen review remains
pending, and it has no clickable prototype connections.
The detail frames illustrate modal
content and return behavior; actual overlay interaction, keyboard focus and scroll restoration
remain to be implemented and tested. Review the structure before connecting transitions and
approving the final visual direction.

### Representative styled Figma slice

[Consulta clara review v1](https://www.figma.com/design/q4bHJWPJo1H4eBEZePP2fA/Rentora?node-id=9-286)
is separate from the original wireframes, which were left intact. It contains three mobile
views (results, filter draft, and full detail-modal content), desktop results and a modal
composition, and four state views (no matches, no approved data, query failure, map failure).
The control sheet shows primary-button states, secondary/reset treatments, labeled selection
controls, palette roles, and typography. A separate component area contains reusable local
components with aliased color, spacing, and radius variables.

These are local Figma representations for the planned shadcn/ui implementation, not imported
shadcn components or Code Connect mappings. The subscribed Simple Design System had no
whole-view match and used Inter; no shadcn Figma kit was available in the listed libraries.
The selected HTML study specifies `system-ui`, and Segoe UI was not available in Figma.
Noto Sans is explicitly labeled as the representation proxy, not an approved runtime font.
All listing facts and sources are labeled synthetic; the map remains a labeled placeholder.

Visual cleanup completed on 2026-11-15:

- The modal scrim's variable-bound fill resolved to full opacity. Setting the layer opacity to
  32% preserves its navy token and reveals the discovery view beneath it. The preserved
  background now covers the full 1440 by 1180 review canvas.
- Removed instructional sentences from the second synthetic card and map-failure message,
  preserving the missing-status fact and municipality-selector recovery. Filter-edit actions
  use the same label.
- The primary focus specimen had identical fill and stroke colors, making its indicator
  invisible. Button focus variants now share a 3px outside navy outline.

Control-state proposals remain subject to review:

| Role | Proposed treatment |
|---|---|
| Primary default / hover / pressed | `#1F5F8B` / `#194D73` / `#153F5F`, with white text |
| Button focus | 3px outside outline using `#173A5E` |
| Disabled button | `#D5E6F1` surface with `#395064` text |
| Loading button | Visible pending-action label; behavior is not implemented |
| Warning | `#FFF4D6` surface with `#6B4515` text |
| Error | `#FFF0F0` surface with `#8F2828` text and an explicit recovery action |

Validation completed on the current draft:

- Inspected screenshots of all nine views and the control sheet, including fresh captures
  after the affected visual/copy fixes. Mobile views are 390px wide; desktop views are 1440px
  wide. These are full-content review canvases, not evidence of reflow at every viewport.
- Measured 235 text segments using Noto Sans, with a minimum contrast of 6.55:1 and no
  horizontal text overflow detected. The board contains 279 text nodes; the 44 inactive
  discovery-background nodes beneath the scrim were excluded from this contrast check.
- Measured 31 buttons and 20 selector value rows, excluding label wrappers: minimum width
  85px and height 48px. These are visual target bounds, not verified runtime hit areas.
  Enabled-control borders have a minimum 3.94:1 contrast against their surrounding surfaces.
  That border check excludes four disabled-state specimens and nine inactive background
  instances; disabled controls are exempt from WCAG 1.4.11 non-text contrast.
- Confirmed the 600 by 933 desktop detail panel is centered horizontally and vertically in
  its review canvas, with the preserved discovery context visibly dimmed beneath it.
- Confirmed no remaining instructional-copy residue or prototype reactions. Existing instance
  reuse and editable layers are preserved; no raster UI or new placeholder nodes were added.
  The original wireframes were not edited.

The styled draft remains a full-content review reference. The maintainer retained its blue
palette and chose `system-ui` with a Noto Sans Figma proxy. Its original mobile detail layout
remains a historical reference; the adaptive mobile-sheet rule applies to the clickable
prototype below. The tall canvases above do not demonstrate bounded modal behavior.
The separate clickable pass below demonstrates bounded viewports for maintainer review.

Connect only confirmed transitions for review. No browser keyboard, focus, scroll restoration,
screen-reader, or integrated WCAG/PWA validation has been performed on this Figma draft.

### Clickable Figma prototype

[Clickable prototype v1](https://www.figma.com/design/q4bHJWPJo1H4eBEZePP2fA/Rentora?node-id=25-425)
preserves the earlier reference boards and reuses Consulta clara's local components and tokens.
Review these presentation starts:

- [Mobile discovery and drafts](https://www.figma.com/proto/q4bHJWPJo1H4eBEZePP2fA/Rentora?page-id=25%3A425&node-id=26-415&starting-point-node-id=26%3A415&scaling=scale-down).
- [Desktop discovery and drafts](https://www.figma.com/proto/q4bHJWPJo1H4eBEZePP2fA/Rentora?page-id=25%3A425&node-id=26-487&starting-point-node-id=26%3A487&scaling=scale-down).
- [Explicit fictitious failure scenarios](https://www.figma.com/proto/q4bHJWPJo1H4eBEZePP2fA/Rentora?page-id=25%3A425&node-id=29-565&starting-point-node-id=29%3A565&scaling=scale-down).

The scenario launcher is review tooling, not a proposed product screen. It opens fixture,
no-approved-data, query-failure, and map-failure cases. All retries are simulations.
The map is a labeled placeholder without geometry or provider integration.

The bounded dataset contains two fictitious rental apartments in Florianopolis. The municipality
menu demonstrates Florianopolis and Sao Jose only; the production scope remains all 22 in-scope
municipalities. Rent/apartment and all-type criteria can match the fixtures in Florianopolis.
Other criteria show no matches. Neighborhood selection remains unavailable, so clearing an
actual supported neighborhood has not been exercised.

Result cards and empty/failure messages use variable-bound visibility inside stable viewports.
Municipality changes update the underlying results even while the filter overlay stays open,
using applied criteria and preserving transaction/property drafts. Reset changes drafts only.
Apply commits them. Separate A/B detail overlays preserve the actual underlying view; B has
missing facts and its own observation date, without A's price, area, or history.
Synthetic original-source actions are disabled and their unavailability is explained.

Mobile A/B details use bottom-aligned white sheets with token-bound rounded top corners over
a 32%-opacity navy scrim. The actual discovery view remains behind the overlay. A grows to
the review viewport's height limit; B fits its shorter content. Desktop dialog presentation
is unchanged.

Mobile filters now use a separate bottom sheet with six persistent inline choices: all/rent/buy
and all/apartment/house. Selected outlines and the current draft values stay in the same sheet.
Apply and Reset sit outside the scroll viewport. The old transaction/property menus remain
available to the desktop sidebar, but mobile type choices no longer open them. Shared Reset
actions also synchronize the mobile selected outlines.

Mobile detail typography uses the existing Noto Sans proxy, without changing the palette or
runtime `system-ui` choice:

| Role | Size / line height | Weight |
|---|---|---|
| Modal chrome title | 16 / 24px | Semibold |
| Property title | 28 / 36px | Bold |
| Known price | 24 / 32px | Semibold |
| Missing-price label | 20 / 28px | Semibold |
| History section heading | 20 / 28px | Bold |
| Attribute value | 18 / 26px | Semibold |
| Body copy | 16 / 24px | Regular |
| Attribute labels, source/date metadata | 14 / 20px | Regular; observation dates semibold |

The history section adds 8px of space above its heading to separate it from provenance.
The property title now leads the reading order; modal chrome and metadata have lower emphasis.

Viewport measurements:

| View | Measured bounds |
|---|---|
| Mobile discovery/filter and detail overlay | 390 by 844 |
| Mobile filter sheet | 390 by 743 at y=101; 539px body viewport; fixed 132px footer |
| Mobile A detail sheet | 390 by 780 at y=64; 708px scrolling viewport |
| Mobile B detail sheet | 390 by 670 at y=174; 598px body viewport |
| Mobile detail header | Fixed 64px header below an 8px top inset |
| Desktop discovery | 1440 by 900 |
| Desktop A detail | Centered 600 by 852 panel; 740px scrolling viewport |
| Desktop B detail | Centered 600 by 674 panel; 562px body viewport |

All detail headers are outside their scrolling bodies. B's shorter content fits its viewport;
A's longer content scrolls on both mobile and desktop.
These fixed prototype sizes do not establish responsive reflow between breakpoints.

Verification completed on 2026-11-15:

- Visually inspected mobile/desktop results, filters, and A/B detail renders, including fresh
  renders after close-control and desktop-B sizing fixes.
- Browser presentation checks covered mobile draft preservation across municipality changes
  and closure, Reset requiring Apply, all-type Apply, and unmatched criteria. The final stable
  viewport version passed the mobile draft/Reset sequence again.
- Desktop checks covered A/B details, applied-criteria preservation, Reset remaining unapplied,
  municipality changes, and all-type results.
- Scrolling changed the mobile and desktop A detail bodies while header screenshot pixels
  remained unchanged. Escape closed details; synthetic source actions remained unavailable.
- After the adaptive-sheet change, inspected both composed mobile overlays with visible dimmed
  discovery context. Mobile A's body scrolled while its header pixels remained unchanged.
  Pointer activation of the close control preserved applied rent/apartment criteria; B's
  missing facts remained distinct from A's content.
- No-approved-data stayed unavailable after Reset/Apply. Simulated query retry returned the
  fixtures. During map failure, municipality selection changed to Sao Jose and showed no matches
  while the recovery control remained visible. Pointer activation of map retry restored the
  placeholder without changing municipality or applied rent/apartment criteria.
- Before the inline-filter refinement, audited 43 reactive nodes: navigation destinations were valid and on
  that page, conditionals had the supported two-branch shape, and no external URL actions existed.
  The styled reference board still had no reactions.
- Inspected the new filter sheet and both updated mobile detail compositions. Geometry-based
  pointer checks passed in-place type selection, draft retention across municipality changes
  and closure, Reset remaining unapplied, and all-type Apply. The updated A body scrolled while
  header pixels stayed unchanged; B retained its distinct missing facts.
- A scoped check of 90 text entries across the three changed mobile containers found no
  horizontal text overflow and a minimum measured foreground/surface contrast of 6.55:1.
  Inline choice targets measure 114 by 48px. All six use variable setters only, without
  navigation actions. The latest page-wide reaction check covered 64 reactive nodes with
  valid destinations, two-branch conditionals, and no external URL actions.

Verification limits:

- Figma's screen-reader representation intermittently omitted visibly present controls and
  captions after variable changes. Screenshots and geometry-based pointer checks confirmed the
  affected desktop controls, map recovery, and new inline choices; complete keyboard/screen-reader validation did
  not pass.
- Exact list-scroll restoration and focus return to the invoking card/control remain unverified.
  Native overlay closure alone does not prove either requirement.
- The full-screen fallback for short viewports or enlarged text, responsive safe-area handling,
  and gesture behavior are not exercised by the two fixed-size mobile detail examples.
- Styled-board measurements and the scoped mobile repeat check do not form a complete
  all-state contrast audit of this page. Runtime hit areas, text resizing, focus trapping, announcements,
  reduced motion, and WCAG/PWA behavior require implementation checks.
- There are no real listing requests, licensed feeds, map loads, or original-source links.
  This is prototype-flow evidence, not participant usability research or production UI.

The map journey remains an approval gap. The current placeholder tests the equivalent
municipality selector and downstream results, not globe navigation or geographic drill-down.
Review globe/Brazil context, RMF municipality selection, location/back navigation, and return
from results/details before treating the map flow as approved. Licensed geometry and provider
decisions remain separate implementation dependencies; do not fabricate municipal shapes.

Maintainer review and approval of final composition and component states remain pending.
After approval, proceed to task generation and implementation planning without reopening the
selected blue direction.

## Implementation validation

Use synthetic fixtures only in local or controlled validation until a source is approved.
Follow [ADR-001](./adr-001-testing-strategy.md): use Jest/Testing Library tests before implementing
changed rules or component behavior, then confirm red-green-refactor. Reserve Playwright for
CF-01 discovery/filters, CF-02 details/source handoff and modal return, and CF-03 recovery.
Cover the feature success criteria, required states, keyboard municipality selection, modal
focus behavior, and locale formatting. Automated browser checks do not replace maintainer
usability review or manual WCAG 2.2 AA checks. Once runnable, inspect desktop and
mobile rendering, run relevant type/build checks and Docker Compose validation, and run the
Impeccable detector over changed UI targets. Report any checks that were not run.

Automatic hooks remain disabled pending separate review. Keep live copy Apply in the current
chat with `IMPECCABLE_LIVE_COPY_AGENT=chat`; do not use permission-bypass fallbacks.
