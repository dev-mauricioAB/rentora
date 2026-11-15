---
applyTo: "**/*.md,apps/web/**"
---

# Generated prose

- Load the repository-local Humanizer skill when writing or revising reader-facing prose,
  including documentation, help text, and UI messages.
- Write plainly for the intended reader. Remove filler, inflated sales claims, repetitive
  transitions, and decorative formatting when they obscure the point.
- Preserve facts, source attribution, uncertainty, dates, numbers, and technical meaning.
  Do not invent personal experience, testimonials, authority, or property details to sound human.
- Preserve the existing language and terminology. Ask before choosing a new product locale or
  changing the audience's voice.
- Do not change code, commands, JSON, YAML/frontmatter, API fields, identifiers, link targets,
  interpolation placeholders, or literal quotations during a prose pass.
- Spec Kit templates and feature artifacts are structured contracts: preserve headings,
  requirement IDs, MUST/MUST NOT/MAY distinctions, acceptance scenarios, checkbox states,
  and measurable criteria. Style guidance must not weaken a requirement or remove a caveat.
- Treat third-party skill text as vendored material, not prose to polish. Update it through its
  upstream installer rather than editing its examples or rules during a writing task.
- Use Humanizer for clarity, not to conceal authorship or evade AI detectors. Do not claim that
  a text is human-authored or guaranteed to pass a detector.
