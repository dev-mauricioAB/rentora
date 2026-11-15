# Rentora agent guidance

## Read the project context

- Follow `.specify/memory/constitution.md`. Use *Fundamentals of Software Architecture: An
  Engineering Approach* and Clean Code as guidance for fit-for-purpose, simple architecture.
- Read the active feature's `spec.md` before making product changes, then its `plan.md`, relevant
  contracts, and `tasks.md` when present. Feature documents live under `specs/`.
- Inspect the repository before drawing conclusions. Separate facts from assumptions. Ask the
  maintainer when a material requirement or design decision remains uncertain.
- This repository currently contains planning documents and agent tooling, not a runnable app.
  Planned commands are not verified commands until the corresponding files and scripts exist.

## Keep the Phase 1 boundaries

- Rentora is a read-only property discovery PWA for the official Florianopolis metropolitan
  region. Guest browsing, factual cards, source attribution, and dated status history are in scope.
- Do not add accounts, reviews, comments, scores, rankings, property pins, or exact addresses and
  coordinates to Phase 1.
- External data requires documented access and reuse permission. Public visibility is not
  permission. Do not scrape candidate sources or bypass access controls.
- Preserve uncertainty, provenance, observation dates, and missing-data states. Never invent a
  property fact or present stale information as current.

## Implement and verify

- Follow the pnpm monorepo plan and its package boundaries. Keep domain rules independent of
  React, NestJS, Prisma, and map providers.
- Keep changes focused; reuse existing helpers and components. Preserve type safety and handle
  failures explicitly.
- Use TDD (red-green-refactor) for new/changed behavior and bug fixes: run a focused failing
  test before implementation, confirm the expected behavioral failure, then implement and
  refactor with tests green. Broken loaders/dependencies are not valid red evidence.
- Use Jest for unit/component and isolated integration tests across the workspace. Use
  Playwright only for the critical journeys in
  `specs/001-aggregate-property-listings/adr-001-testing-strategy.md`; retain manual
  usability/accessibility review. Do not add Vitest as a second unit runner.
- Future task generation must include runner/CI setup and failing-test prerequisites before
  dependent behavior work. Do not treat behavior tests as optional or defer them to polish.
- Validate changed behavior with focused tests and existing checks. Docker Compose must support
  local web/API/database build validation when implemented. Never commit local credentials.
- Do not claim success for commands or visual checks that were not run. Report blockers plainly.

## Use the installed skills

- For frontend design, implementation, or review, load `.github/skills/impeccable/SKILL.md` and
  the relevant playbook. Follow `.github/instructions/frontend.instructions.md`.
- For writing or revising reader-facing prose, load `.agents/skills/humanizer/SKILL.md`.
  Follow `.github/instructions/writing.instructions.md`, including for UI copy.
- Skills improve craft; they do not replace requirements, accessibility checks, tests, factual
  verification, or maintainer approval. Do not rewrite unrelated documents as a side effect.
- Review third-party scripts before execution. Do not enable hooks, launch bypass-permission
  agents, or send repository content to external services without approval. For Impeccable live
  mode, keep copy Apply in the current chat with `IMPECCABLE_LIVE_COPY_AGENT=chat`.
