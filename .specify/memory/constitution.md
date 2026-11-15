<!--
Sync Impact Report
Version change: 1.0.0 -> 1.1.0 (material expansion of agent and architecture guidance)
Modified principles:
- I. User Value and Correctness -> I. Evidence, Clarification, and User Value
- II. Security and Privacy by Default -> III. Security, Privacy, and Authorized Data
- III. Verifiable Quality -> IV. Verifiable Quality
- IV. Maintainable Simplicity -> II. Fitness-for-Purpose Architecture and Clean Code
- V. Reliable Delivery -> V. Reliable and Maintainable Delivery
Added sections: None
Removed sections: None
Follow-up TODOs: Confirm the original ratification date; document product scope and preferred
architecture in their respective project documents.
-->

# Rentora Constitution

## Core Principles

### I. Evidence, Clarification, and User Value
Every feature MUST address a defined user need and have verifiable acceptance criteria. AI agents
MUST inspect relevant project sources and distinguish verified facts from assumptions. If an
agent remains unsure after that inspection, or the request has multiple reasonable
interpretations, it MUST ask the user rather than guess. Agents MUST NOT invent requirements,
repository behavior, or external facts. Existing project conventions may resolve a choice only
when they clearly apply.

### II. Fitness-for-Purpose Architecture and Clean Code
Architecture decisions MUST use *Fundamentals of Software Architecture: An Engineering Approach*
as a guiding reference and MUST be evaluated against the project's requirements, quality
attributes, constraints, and trade-offs. Significant decisions and their rationale MUST be
documented. Clean Code is guidance for clear names, cohesive responsibilities, readable logic,
and maintainability; it MUST be applied in context, not as a reason for unnecessary abstraction
or changes that conflict with established project patterns.

### III. Security, Privacy, and Authorized Data
Code MUST validate untrusted input at system boundaries, enforce least privilege, minimize
collection of personal data, protect credentials and sensitive data, and avoid exposing sensitive
values in logs or errors. Public availability of information MUST NOT be treated as permission to
collect or republish it. Before implementing external data access, contributors MUST verify the
applicable terms, legal basis, and approved access method; they MUST NOT bypass access controls.
Data presented to users MUST retain source attribution and provenance where applicable.

### IV. Verifiable Quality
Behavior changes MUST be covered by focused automated tests, and bug fixes MUST include a
regression test when practical. Before integration, contributors MUST run the narrowest relevant
tests and checks and address failures caused by their changes. Tests MUST verify requirements and
preserve established behavior, not merely mirror implementation details.

### V. Reliable and Maintainable Delivery
Changes MUST handle expected failure cases explicitly and MUST NOT silently convert errors into
success-shaped results. User-visible behavior and operationally important failures MUST be
observable through the project's established reporting mechanisms. Changes MUST use the simplest
implementation that satisfies requirements; new abstractions, dependencies, and configuration
MUST have a concrete need. Non-obvious behavior and significant trade-offs MUST be documented.

## Engineering Constraints

Rentora is intended to be a pnpm-managed monorepo delivering a progressive web application.
Package boundaries and dependency relationships MUST follow documented workspace decisions.
Specific frameworks, libraries, hosting providers, and database services are preferred
architecture choices, not immutable constitutional requirements; they MUST be evaluated and
recorded in project planning or architecture documentation before implementation. User interfaces
MUST meet applicable accessibility requirements, and performance or compatibility targets MUST
be verified when the project defines them.

## Development Workflow

Before integration, changes MUST be reviewed for requirement coverage, security and privacy
impact, relevant tests, and directly related documentation. Changes affecting data formats,
interfaces, or established behavior MUST include a migration or compatibility plan where needed.
AI agents MUST pause and request clarification instead of making a material decision from
uncertain or missing information. Reviewers and implementers MUST identify any constitution
deviation and obtain maintainer approval with a documented rationale before accepting it.

## Governance

This constitution governs project development practices. Amendments MUST be proposed with their
rationale and impact, reviewed and approved by the project maintainer, and reflected in this
document. Each amendment MUST update the version and last-amended date. Versioning follows
Semantic Versioning: MAJOR for incompatible governance changes, MINOR for new principles or
materially expanded requirements, and PATCH for clarifications and non-semantic refinements.
Contributors and reviewers MUST assess relevant changes for compliance; approved deviations
require an explicit rationale. The constitution MUST be reviewed when project requirements or
engineering practices materially change.

**Version**: 1.1.0 | **Ratified**: TODO(RATIFICATION_DATE): confirm original adoption date | **Last Amended**: 2026-11-15
