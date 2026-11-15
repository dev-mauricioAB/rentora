# Agent tooling

Rentora uses repository-scoped Copilot instructions and two third-party skills. They guide agents;
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
and verify both skills are available. Do not duplicate these rules into another root agent file.

## Installed skills

| Skill | Location | Upstream | License |
|---|---|---|---|
| Impeccable 4.9.1 | [.github/skills/impeccable](./skills/impeccable/) | [pbakaus/impeccable](https://github.com/pbakaus/impeccable) | Apache-2.0 |
| Humanizer 4.0.0 | [.agents/skills/humanizer](../.agents/skills/humanizer/) | [blader/humanizer](https://github.com/blader/humanizer) | MIT |

Impeccable was installed using the Copilot-specific distribution, including its companion agents.
Humanizer uses the shared `.agents/skills` location supported by Copilot and is recorded in
[skills-lock.json](../skills-lock.json). Keep upstream license notices with the installed files.

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
