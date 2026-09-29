# AGENTS.md

This file defines the default working agreement for contributors and AI agents in this repository.

These rules are intentionally project-agnostic.

Project-specific information belongs in `docs/project.md`.

A more specific `AGENTS.md` inside a subdirectory may extend or override these rules only for that scope.

## Core Principles

Always:

1. Understand the existing project before changing it.
2. Plan non-trivial work before implementation.
3. Prefer maintainable solutions over clever solutions.
4. Use architecture appropriate to the current problem and scale.
5. Keep documentation synchronized with implementation.
6. Record material changes in `CHANGELOG.md`.
7. Verify actual behavior instead of assuming compilation means correctness.
8. State uncertainty, limitations, and failed verification honestly.
9. Preserve existing behavior unless the task intentionally changes it.
10. Avoid unrelated cleanup while implementing a scoped change.

Never invent:

- requirements
- APIs
- implementation state
- test results
- deployment status
- product behavior
- external service capabilities

If something is unknown, investigate it when practical.

If it cannot be verified, say so explicitly.

## Project Context

Before meaningful work, read the relevant repository context:

```text
AGENTS.md
docs/project.md
relevant files under docs/
CHANGELOG.md
package/workspace configuration
existing implementation
relevant tests
```

If `docs/project.md` does not exist, inspect the repository and create it before starting the first non-trivial task.

Do not ask the repository owner to manually describe information that can be determined reliably from the repository.

Keep `docs/project.md` synchronized when any of these change:

- product purpose
- technology stack
- architecture
- repository layout
- development commands
- external services
- major constraints
- important project conventions

Repository documentation and existing architecture take precedence over generic assumptions about how a project should be structured.

Do not replace an established pattern merely because another approach is more popular.

## Skill Policy

Repository-specific workflows may be stored under:

```text
.agents/skills/<skill-name>/SKILL.md
```

Do not create generic skills preemptively.

Create a repository-local skill when at least one of these is true:

- a workflow has repeated and is easy to perform inconsistently
- the workflow crosses multiple architectural or service boundaries
- a safety, security, deployment, migration, or data-integrity process needs a fixed checklist
- visual or behavioral verification requires a repeatable procedure
- stable repository-specific knowledge would otherwise need to be explained repeatedly
- the same sequence of inspection, implementation, and verification is repeatedly required

Do not create a skill for:

- one-off implementation work
- generic coding practices already covered by `AGENTS.md`
- obvious framework usage
- speculative future workflows

Skills should be narrow and repository-specific.

Prefer specific names such as:

```text
project-storybook-visual-qa
project-git-workflow
project-auth-architecture
```

over generic names such as:

```text
verify-ui
git
architecture
```

When a matching skill exists, read its complete `SKILL.md` before acting.

If a skill becomes too broad, split it by responsibility instead of continuously adding unrelated rules.

## Documentation

Use Markdown for committed repository documentation.

Keep durable project knowledge under `docs/`.

Do not allow important decisions or architecture knowledge to exist only in chat history.

### Plans

For non-trivial work, create a working plan under:

```text
docs/plans/<yyyy-mm-dd>-<short-description>.md
```

A plan should normally describe:

```md
# <Task>

## Goal

## Current State

## Scope

## Non-Goals

## Constraints

## Decisions

## Architecture

## Implementation Plan

## Verification

## Risks / Open Questions

## Result
```

Plans are working documents, not one-time proposals.

Update the active plan when:

- assumptions change
- implementation differs from the original plan
- scope changes
- new constraints appear
- architecture decisions change
- verification reveals additional work

When work is complete, update `Result` so the document describes what was actually implemented.

Do not create planning documents for trivial typo, formatting, or purely mechanical changes.

### Architecture Decisions

Long-term architecture decisions should be recorded under:

```text
docs/adr/
```

Create an ADR only when the decision has meaningful long-term consequences or is likely to require future explanation.

Do not create ADRs for routine implementation choices.

## Architecture

Use the smallest architecture that clearly supports the current requirements.

Optimize for:

- clear responsibility
- predictable dependency direction
- focused modules
- explicit boundaries
- local reasoning
- maintainability
- testability
- replaceable infrastructure

Do not introduce abstractions solely for hypothetical future requirements.

Do not automatically introduce:

- controller/service/repository layering
- microservices
- message queues
- event buses
- Redis
- additional databases
- global state management
- dependency injection frameworks
- generic factories
- wrapper layers

Every additional layer must solve a concrete current problem.

Prefer adapting the repository's existing architecture before introducing another architectural model.

Keep business logic away from framework, transport, persistence, and presentation code when the distinction is meaningful.

Framework-specific, vendor-specific, database-specific, and transport-specific behavior should remain near its boundary.

Prefer intentional public APIs over importing module internals across architectural boundaries.

Avoid circular dependencies.

Architecture should make invalid dependencies difficult rather than relying entirely on developer discipline.

## Dependencies and Services

Before adding a dependency, determine whether the requirement can be solved clearly using:

1. existing project capabilities
2. platform or standard-library capabilities
3. an existing installed dependency
4. a maintained open-source dependency
5. an external service

Prefer free and open-source solutions for toy projects.

When an external service is useful, prefer a sufficient free tier unless a paid option provides a concrete product or development benefit.

Before adopting a service, consider:

- free-tier limits
- long-term pricing
- vendor lock-in
- privacy
- data ownership
- rate limits
- operational complexity
- migration difficulty
- local development support

Never silently choose a paid-only service.

If the free option has important limitations, explain them instead of hiding the tradeoff.

Do not recreate a mature library merely to avoid adding one reasonable dependency.

Likewise, do not add a dependency for behavior that can be implemented clearly and safely with a small amount of existing code.

Add dependencies to the smallest scope that owns them.

Remove dependencies that become unused.

## TypeScript and JavaScript

Use modern stable language features supported by the repository's configured runtime and toolchain.

Prefer:

- `const`
- arrow functions
- strict TypeScript
- narrow types
- explicit types at public boundaries
- inference for obvious local values
- immutable transformations when they improve clarity

For normal functions, handlers, helpers, hooks, factories, and React components, prefer:

```ts
const example = () => {
  // ...
};
```

over:

```ts
function example() {
  // ...
}
```

Use function declarations only when there is a concrete reason such as:

- framework or API requirements
- intentional hoisting
- generators
- materially improved readability

For modules with one clear primary component or implementation, this pattern is preferred when compatible with the surrounding repository:

```ts
const Example = () => {
  return null;
};

export default Example;
```

Prefer named exports for:

- libraries
- domain APIs
- shared utilities
- files with multiple meaningful exports

Framework-required export conventions take precedence.

Naming defaults:

- React components: `PascalCase`
- exported types: `PascalCase`
- functions and values: `camelCase`
- true process-wide constants: `SCREAMING_SNAKE_CASE`
- source files and directories: `kebab-case`

Framework-reserved filenames are exempt.

Avoid `any` when a reasonable type can be expressed.

Avoid type assertions used merely to silence TypeScript.

Do not use outdated syntax or APIs when the configured runtime supports a clearer modern equivalent.

## Files and Modules

Keep files focused around a clear responsibility.

Split code by responsibility, not arbitrary line count.

Do not create tiny wrapper files that provide no meaningful boundary.

Avoid generic directories such as `utils`, `services`, or `helpers` becoming dumping grounds.

Place code near the feature or domain that owns it when practical.

Do not create barrel files by default.

Use barrel files only when they define an intentional public API or materially improve a module boundary.

Do not duplicate existing functionality instead of reusing its authoritative implementation.

## Comments

Prefer self-explanatory code.

Do not add comments that merely narrate syntax or restate the implementation.

Do not add decorative section comments.

Do not generate verbose JSDoc for straightforward code.

Comments should explain things such as:

- why a decision exists
- an invariant
- a compatibility workaround
- a security constraint
- a non-obvious external limitation
- behavior that would otherwise be easy to accidentally break

## UI and UX

User-facing interfaces should look intentionally designed for the specific product.

Reuse the existing visual language before introducing a new one.

Avoid generic AI-generated interface patterns.

Do not default to:

- cards for every section
- cards nested inside cards
- excessive rounded containers
- excessive pills or badges
- unnecessary gradients
- decorative glow effects
- glassmorphism without a product reason
- oversized hero headings inside application screens
- fake statistics
- decorative charts without product value
- random emoji as product icons
- excessive explanatory copy
- unnecessary animation
- excessive whitespace that reduces useful information density
- identical visual hierarchy for every section

Do not use visual effects merely to make an interface appear polished.

Build hierarchy primarily through:

- typography
- spacing
- alignment
- grouping
- dividers
- restrained borders
- surface depth
- deliberate color
- appropriate content density

Use cards when a card represents a meaningful standalone object or grouping.

Do not use cards merely because content needs a container.

### Product Copy

User-facing copy should be concise, direct, and appropriate to the product domain.

Avoid generic generated language such as:

```text
Seamlessly manage...
Unlock the power of...
Take your experience to the next level...
Everything you need in one place...
Effortlessly...
```

Prefer terminology actual users of the product would use.

Do not explain obvious interface behavior.

Error messages should communicate:

1. what happened
2. what the user can do next, when applicable

### Interface States

For meaningful user-facing flows, deliberately consider applicable:

- loading
- empty
- failure
- partial failure
- not found
- disabled
- pending
- success

Do not show fake data merely to avoid an empty interface.

Do not hide failed operations behind optimistic success states.

Interactive elements should provide appropriate:

- hover
- active
- focus
- disabled
- pending states

### Accessibility

Preserve applicable:

- semantic HTML
- keyboard navigation
- visible focus
- accessible labels
- sufficient color contrast
- sensible heading hierarchy
- usable touch targets
- reduced-motion behavior

Do not remove focus indicators without an accessible replacement.

### Responsive Design

Do not treat mobile as desktop scaled smaller.

Check applicable:

- text wrapping
- overflow
- navigation
- forms
- tables
- dialogs
- button groups
- sticky elements
- minimum usable widths

A deliberate limited mobile fallback is acceptable for dense desktop tools when full mobile support is not a product requirement.

### Motion

Use motion to communicate:

- state
- causality
- progress
- hierarchy
- spatial continuity

Do not animate merely because an animation library is available.

Keep motion restrained.

When browser or visual verification is available, inspect the actual rendered interface instead of judging UI quality only from source code.

For meaningful layout changes, verify at least:

- one normal desktop viewport
- one narrow viewport

## State and Data

Keep state at the smallest appropriate scope.

Prefer:

- remote-state tools for authoritative server state
- local component state for local interaction
- URL state for meaningful navigation state
- persistent browser storage only when persistence is an actual product requirement

Do not introduce global state merely to avoid passing a small amount of data.

Do not duplicate authoritative remote data into independent client state without a concrete reason.

Treat external input as untrusted.

Validate applicable:

- user input
- external APIs
- webhooks
- uploaded files
- environment variables
- persisted legacy data
- URL parameters

Avoid unbounded:

- collections
- payloads
- retries
- polling
- background operations

## Error Handling

Failures must remain visible.

Do not:

- swallow errors
- return fake successful results
- silently insert placeholder data
- hide partial failure behind success
- retry indefinitely
- claim persistence before it succeeded

Fallback behavior must be intentional.

When failures are recoverable by the user, provide a reasonable recovery path.

Diagnostics should contain enough context to investigate a problem without exposing secrets or unnecessary personal information.

## Security and Privacy

Never commit:

```text
.env*
credentials
access tokens
refresh tokens
private keys
production user data
raw sensitive payloads
```

Do not expose server-only secrets through client-side environment variables.

Do not log:

- authentication tokens
- cookies
- credentials
- private keys
- unnecessary personal information

Persist only data the product actually needs.

When introducing persisted sensitive or diagnostic data, consider:

- purpose
- sensitivity
- access
- retention
- deletion
- redaction

Prefer collecting less data.

## CHANGELOG

Maintain the root `CHANGELOG.md`.

Use Keep a Changelog style unless the project already defines another consistent format.

Material changes belong under `Unreleased` in the same change set.

Record:

- user-facing features
- behavior changes
- bug fixes
- material architecture changes
- compatibility changes
- security changes
- operational changes
- meaningful infrastructure changes

Do not record:

- formatting-only changes
- trivial internal cleanup
- generated output
- mechanical refactors with no meaningful effect

Use applicable sections:

```md
## [Unreleased]

### Added

### Changed

### Fixed

### Deprecated

### Removed

### Security
```

Do not rewrite historical entries except to correct inaccurate information.

## Scope Discipline

Solve the requested problem.

Do not silently expand a task into:

- unrelated refactoring
- large cleanup
- design-system replacement
- dependency upgrades
- architecture migration
- infrastructure changes
- speculative future functionality

If nearby technical debt blocks the requested work, change the smallest necessary area and document why.

Do not rewrite working code solely to match a preferred style.

Before replacing existing implementation:

1. understand what it does
2. inspect where it is used
3. inspect relevant tests
4. identify compatibility behavior
5. confirm the replacement solves an actual problem

Preserve unrelated existing work.

## Verification

Run the smallest relevant checks during development.

Before considering meaningful work complete, run the appropriate repository checks when practical.

These may include:

- formatting
- lint
- typecheck
- unit tests
- integration tests
- architecture or boundary checks
- build
- browser verification

Compilation alone does not prove a feature works.

Where practical, verify the feature through the same entry point a real user uses.

For UI changes, verify the rendered interface.

For API changes, verify the actual request path.

For persistence changes, verify the actual persistence path.

For authentication changes, verify applicable authenticated and unauthenticated behavior.

For migrations, verify the migration path rather than only inspecting the final schema.

Do not report a verification step as successful unless it was actually executed successfully.

If verification cannot be performed, state:

- what was verified
- what was not verified
- why it could not be verified
- what remains uncertain

When relevant, distinguish:

```text
implemented locally
verified locally
committed
pushed
available in preview
verified in preview
deployed to production
verified in production
```

Do not use an earlier state as evidence for a later one.

A successful build does not prove runtime behavior.

A successful push does not prove deployment.

A Preview deployment does not prove Production behavior.

## Git

Repository Git and pull request conventions are defined in:

```text
docs/git-convention.md
```

Read that document before:

- creating branches
- creating commits
- pushing
- opening or modifying pull requests
- changing Git history
- performing other Git or GitHub mutations

`docs/git-convention.md` is the source of truth for Git workflow details.

Do not duplicate repository Git conventions in this file.

Read-only Git inspection may be used when needed to understand repository state.

## Honesty and Uncertainty

Never fabricate confidence.

If something is uncertain:

1. identify what is uncertain
2. determine what evidence is missing
3. investigate when practical
4. state the limitation if it remains unresolved

Do not pretend that:

- a service supports something that was not verified
- an API exists because it seems likely
- a UI works because it compiled
- a migration works because the schema looks correct
- a deployment succeeded because a push succeeded
- a test passed because the implementation appears correct

If an implementation contains a compromise, explain the compromise.

If a library, framework, or service does not support the required behavior cleanly, say so.

If the requested design has meaningful drawbacks, identify them rather than silently hiding them.

When no clean solution exists, explain the available tradeoffs.

A partially verified truthful result is better than a confidently incorrect one.

## Definition of Done

A meaningful change is complete only when:

- the requested behavior is implemented
- repository architecture remains coherent
- unnecessary complexity was not introduced
- relevant failure and edge cases were considered
- applicable checks pass
- actual behavior was verified where practical
- the active plan reflects the final implementation
- `docs/project.md` remains accurate
- other affected documentation remains accurate
- `CHANGELOG.md` contains material changes
- temporary or generated artifacts are not accidentally committed
- no secrets were introduced
- remaining limitations are stated honestly
- another contributor can continue the work without relying on hidden context

A green build alone is not the definition of done.