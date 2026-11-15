# Agent tooling

Rentora uses repository-scoped Copilot instructions and six third-party skills. They guide agents;
they are not application dependencies or a guarantee of generated code quality.

## Instruction layout

- [copilot-instructions.md](./copilot-instructions.md): repository-wide product and engineering
  constraints, context reading order, and skill routing.
- [frontend.instructions.md](./instructions/frontend.instructions.md): path-specific UI guidance.
- [writing.instructions.md](./instructions/writing.instructions.md): prose and UI copy guidance.
- [.specify/memory/constitution.md](../.specify/memory/constitution.md): project governance.
- [specs/](../specs/): feature requirements, architecture, contracts, and validation guides.

Path-specific instructions apply only when their glob matches the files in the task. Repository
instructions also route skills for design or writing requests with no matching file yet.
Copilot instruction and skill support varies by client; reload VS Code/Copilot after installation
and verify the skills are available. Do not duplicate these rules into another root agent file.

## Installed skills

| Skill | Location | Upstream | License |
|---|---|---|---|
| Impeccable 4.9.1 | [.github/skills/impeccable](./skills/impeccable/) | [pbakaus/impeccable](https://github.com/pbakaus/impeccable) | Apache-2.0 |
| Humanizer 4.0.0 | [.agents/skills/humanizer](../.agents/skills/humanizer/) | [blader/humanizer](https://github.com/blader/humanizer) | MIT |
| NestJS best practices 1.2.0 | [.agents/skills/nestjs-best-practices](../.agents/skills/nestjs-best-practices/) | [Kadajett/agent-nestjs-skills](https://github.com/Kadajett/agent-nestjs-skills/tree/3986e0cede33958e000f959031cbee0cd83c2941/skills/nestjs-best-practices) | MIT declared in SKILL.md |
| TanStack Query best practices | [.agents/skills/tanstack-query-best-practices](../.agents/skills/tanstack-query-best-practices/) | [DeckardGer/tanstack-agent-skills](https://github.com/DeckardGer/tanstack-agent-skills/tree/b2fc14c974b2ca09b38de09c28237f240175205b/skills/tanstack-query) | MIT |
| Playwright best practices 1.2 | [.agents/skills/playwright-best-practices](../.agents/skills/playwright-best-practices/) | [currents-dev/playwright-best-practices-skill](https://github.com/currents-dev/playwright-best-practices-skill/tree/0b75b5835a87de1fdd202ba965f3bc5a27738fdf/playwright-best-practices) | MIT |
| Vercel React best practices 1.0.0 | [.agents/skills/vercel-react-best-practices](../.agents/skills/vercel-react-best-practices/) | [vercel-labs/agent-skills](https://github.com/vercel-labs/agent-skills/tree/1a2c79d4e6c4a2283dc2c614b6538cbe1ed5ca62/skills/react-best-practices) | MIT declared in SKILL.md |

Impeccable was installed using the Copilot-specific distribution, including its companion agents.
Humanizer and the four engineering skills use the shared `.agents/skills` location supported
by Copilot and are recorded in [skills-lock.json](../skills-lock.json). The four new entries
include source commits and content hashes. Keep upstream license notices; the additional
repository-level notices are preserved in
[THIRD_PARTY_NOTICES.txt](../.agents/THIRD_PARTY_NOTICES.txt). NestJS and React declare MIT in
their skill frontmatter but supply no standalone license file in the installed source revisions.

### Installation and deliberate updates

Run from the repository root:

```powershell
npx --yes impeccable install --providers=github --project --no-hooks --yes
$env:DISABLE_TELEMETRY = '1'
npx --yes skills add blader/humanizer --agent github-copilot --skill humanizer --copy --yes
```

These commands fetch the current upstream versions; they are not a frozen reinstall of
Impeccable 4.9.1. Review the resulting diff and version changes before accepting an update.
Do not modify global skill installations for a repository task.

To restore the four engineering skills at the recorded commits, use Skills CLI 1.8.1 from
the repository root:

```powershell
$env:DISABLE_TELEMETRY = '1'
$env:DO_NOT_TRACK = '1'
$env:npm_config_ignore_scripts = 'true'
$lock = Get-Content .\skills-lock.json -Raw | ConvertFrom-Json
$names = @('nestjs-best-practices', 'tanstack-query-best-practices',
    'playwright-best-practices', 'vercel-react-best-practices')
foreach ($name in $names) {
    $entry = $lock.skills.$name
    $folder = $entry.skillPath -replace '/SKILL\.md$', ''
    $source = "https://github.com/$($entry.source)/tree/$($entry.ref)/$folder"
    npx --yes skills@1.8.1 add $source --skill $name --agent github-copilot --copy --yes
    if ($LASTEXITCODE -ne 0) { throw "Skill installation failed: $name" }
}
```

Run this in a dedicated shell so its environment settings do not affect unrelated work.
Installation copies guidance and references; it does not authorize executing their examples,
adding application dependencies, or enabling hooks. Review upstream changes and notices before
deliberately updating a recorded revision. Do not edit vendored skill prose to fix conflicts;
apply the repository's requirements and routing constraints.

The Impeccable launcher pins its engine in `scripts/VERSION` and verifies downloads against
`scripts/engine-checksums.txt`. Its platform-specific binary is ignored by Git and can be restored
by the launcher. On Windows use:

```powershell
& .github\skills\impeccable\scripts\impeccable.cmd --help
```

## Before frontend implementation

1. Review the feature spec, plan, source-permission gates, [PRODUCT.md](../PRODUCT.md), and the
   approved [frontend brief](../specs/001-aggregate-property-listings/frontend-brief.md).
2. Product setup is complete: `pt-BR` initially with preparation for future locale switching,
   and WCAG 2.2 AA. Revisit `/impeccable init` only when product context changes.
3. Obtain visual-direction and final-composition approval before user-facing UI code.
   Capture the implemented system with `/impeccable document` when there is actual UI evidence.
4. Use Impeccable for UI work and Humanizer for prose. Still run tests and inspect the UI in a
   browser when available.

The chosen future landing/showcase workflow default is code-first in
[.impeccable/config.json](../.impeccable/config.json). App screens are also code-first.
No DESIGN.md or implemented visual system exists yet. Do not ignore the entire `.impeccable`
directory: confirmed configuration and later design records must be shared with contributors.

Automatic hooks were not installed. Enable them only after reviewing the scripts, client support,
and permissions. Live mode may start other agents if not configured; set
`IMPECCABLE_LIVE_COPY_AGENT=chat` before starting it to keep copy Apply in the current chat.
Do not use a permission-bypass fallback.

Humanizer is an authoring skill, not a backend text-processing service. Runtime handling of
generated property text would need a separate requirement and source/provenance review.

## Engineering skill routing and tests

- NestJS: use relevant module/DI/error/test rules within the planned modular monolith.
  Preserve Prisma and shared Zod contracts; keep framework types out of the domain.
  Authentication, TypeORM, events, and queues in examples do not authorize new scope.
- TanStack Query: use applied criteria in query keys; keep drafts in client state.
  Verify cancellation and retry behavior against installed APIs, distinguish cache freshness
  from source observation dates, and preserve explicit failures. Do not add offline listing
  persistence or replace unavailable data with successful fixture results.
- React: use Vite-compatible rendering/bundle guidance alongside Impeccable. Next.js, RSC,
  and SWR examples do not change the selected stack. Optimize measured problems.
- Playwright: follow only the critical browser journeys in
  [ADR-001](../specs/001-aggregate-property-listings/adr-001-testing-strategy.md).
  Do not add an exhaustive browser suite or suppress accessibility failures.

The accepted testing workflow is Jest red-green-refactor for new/changed behavior and bug
fixes, with isolated integration tests and critical-only Playwright. Manual usability and
accessibility review remain required. The active plan and task template require test-runner/CI
setup and failing-test prerequisites; no application scripts or test suites exist yet.
