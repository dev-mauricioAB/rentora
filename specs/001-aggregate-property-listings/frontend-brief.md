# Frontend implementation brief

Approved behavior and accessibility baseline: 2026-11-15.
Visual identity and final composition: not yet approved.

## Job and outcome

Help a guest renter or buyer narrow a municipality and property type, understand sourced facts
and their dates, then continue to the original listing. This is an Impeccable Operate surface.
Use [PRODUCT.md](../../PRODUCT.md) for audience and language context and
[spec.md](./spec.md) for product requirements. This brief does not override either document.

## Interaction and layout

- On mobile, use a vertical flow: location/map, filters, then one result list.
- On desktop, keep location/filter context beside that same result list. Do not split results
  into multiple neighborhood or property-type groups.
- Preserve the globe-style entry and Brazil/Florianopolis defaults. Only in-scope municipalities
  are selectable. Provide an equivalent labeled keyboard-accessible municipality control.
- Make selected filters and municipality clear. A neighborhood filter is available only when
  authorized data supports it; do not infer neighborhoods.
- Keep filter edits separate from applied criteria. Update results only when the visitor presses
  Apply; do not issue listing requests for each draft filter change.
- Reset clears filter values while keeping the selected municipality. The explicit Apply rule
  also applies to the cleared filter values.
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

## Before the first UI code

1. Read the constitution, feature spec, plan, PRODUCT.md, this brief, and frontend instructions.
2. Use Impeccable's new-work workflow to present and obtain approval for the visual direction
   and final composition. Do not quietly choose colors, fonts, logos, imagery, or map styling.
3. Record the approved visual direction using that workflow. A future DESIGN.md must distinguish
   approved direction from tokens/components actually implemented; do not fabricate shipped UI.
4. Load the relevant playbook and craft floor immediately before UI edits. Build this app
   surface code-first; [.impeccable/config.json](../../.impeccable/config.json) also records the
   chosen code-first default for future landing/showcase surfaces.
5. Reuse UI components and shared styling when they exist; preserve domain/package boundaries.

Scaffolding and task planning may proceed before visual approval. User-facing UI implementation
must wait for that approval. No runnable application or visual identity exists yet.

## Discovery references

Read [public Mobbin flow research](./research.md#public-mobbin-flow-research) before proposing
the visual composition. The inspected Airbnb map/card screen, Glassdoor home/detail previews,
and On/Turo filter descriptions provide related discovery patterns, not usability-validation
results or a Rentora visual identity. Zillow and Redfin's inspected map/results highlights are
the primary housing composition references; Redfin's detail summary is another factual-hierarchy
comparison. Trulia's named search/filter/detail flows remain follow-up targets with metadata-only
evidence. Full discovery transitions remain unverified because catalog access is limited; do not
infer filter/reset, drawer gestures, or return behavior from isolated screenshots. These native
iOS references do not establish the responsive PWA's behavior. Apply/reset and detail-return
behavior were subsequently confirmed with the maintainer and are recorded above.

Preserve this brief's approved behavior and scope. Do not import ratings, favorites, account
requirements, booking, alerts, or exact property pins from comparison apps. Apply/reset
and detail-return behavior follow the confirmed interaction rules, not competitor screenshots.
Municipality-change behavior, including clearing a neighborhood that no longer applies,
still needs confirmation before implementation.

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

This first pass is awaiting maintainer review and has no clickable prototype connections.
The detail frames illustrate modal
content and return behavior; actual overlay interaction, keyboard focus and scroll restoration
remain to be implemented and tested. Review the structure before connecting transitions and
approving the final visual direction.

## Implementation validation

Use synthetic fixtures only in local or controlled validation until a source is approved.
Cover the feature success criteria, required states, keyboard municipality selection, modal
focus behavior, and locale formatting with focused tests. Once runnable, inspect desktop and
mobile rendering, run relevant type/build checks and Docker Compose validation, and run the
Impeccable detector over changed UI targets. Report any checks that were not run.

Automatic hooks remain disabled pending separate review. Keep live copy Apply in the current
chat with `IMPECCABLE_LIVE_COPY_AGENT=chat`; do not use permission-bypass fallbacks.
