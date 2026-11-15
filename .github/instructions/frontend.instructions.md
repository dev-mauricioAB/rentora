---
applyTo: "apps/web/**,packages/ui/**"
---

# Frontend implementation and design

- Load the repository-local Impeccable skill and its relevant playbook before frontend work.
  Use the Windows launcher `impeccable.cmd` on Windows. If context loading is unavailable,
  report that and read existing project context directly; do not invent missing design context.
- Use the active feature spec as product truth. Existing tokens, styles, and components are
  design evidence; do not replace them during a refinement.
- Read root `PRODUCT.md` for confirmed audience, workflow, and localization context.
- Read the active feature's `frontend-brief.md` when present. Scaffolding may proceed before
  visual direction approval; user-facing UI code must wait for that approval. Never describe
  proposed tokens or components as an existing implementation.
- Before the first UI implementation, clarify unresolved visual direction and record approved
  product/design context through Impeccable's init and design workflow. Do not assume a brand,
  palette, fonts, or locale from a reference site.
- Inherit the direction selected in PRODUCT.md and the frontend brief. A missing DESIGN.md
  does not authorize another direction round; revisit the selected world only when the
  maintainer requests a change. Resolve pending composition and state specifications within it.
- Prefer task-focused, readable interfaces for property discovery. Keep one list of factual
  cards, municipality selection, supported neighborhood filters, and a details modal. Do not
  add ratings or exact property locations for visual effect.
- Follow the plan's React 19+, Tailwind, Zustand, TanStack Query, React Router, Zod, and React Hook
  Form preferences. Use Zustand for client-only state and TanStack Query for server state.
- Use the approved shadcn/ui foundation with Tailwind for required common controls. Reuse
  canonical components and approved variants; do not introduce Material UI alongside it in
  Phase 1. Keep UI dependencies in the presentation layer and follow the plan's component
  sourcing and update rules. Default library styles are not an approved visual identity.
- Preserve primitive semantics and behavior when customizing components. Verify accessible
  names, focus handling, and keyboard operation in Rentora's integration; using a component
  library does not establish WCAG compliance.
- Provide semantic controls, keyboard access, visible focus, accessible modal behavior,
  sufficient contrast, and reduced-motion support. Make map selection accessible through an
  equivalent non-pointer control. The confirmed target is WCAG 2.2 AA; verify manually as well
  as with automated checks.
- Handle loading, empty, missing-data, stale-data, and error states without manufacturing facts.
  Preserve visible source attribution and map-provider attribution.
- Use Humanizer for UI copy without changing factual meaning, validation rules, links,
  localization keys, or interpolation placeholders.
- Ship `pt-BR` only initially, but isolate UI messages behind stable localization keys and use
  locale-aware date/number formatting. Prepare for future locale switching without adding
  untranslated languages or a nonfunctional switcher. Do not translate source facts implicitly.
- Inspect the rendered result at desktop and mobile sizes when runnable; test affected
  interactions and run the narrowest relevant build/type checks. Report unverified visual work.
