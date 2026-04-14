---
name: software-architect-writer
description: |
  Software documentation architect. Bridges code and knowledge — designs doc structure, writes and maintains all project docs, and keeps strategic teams informed. Use this skill whenever:
  - Creating or updating any doc file (spec, test doc, architecture, README, doc-map, changelog)
  - Implementing or modifying a feature and need to write or update its documentation
  - Running any documentation command: /plan, /start, /audit, /sketch, /standard, /doc2notion
  - Setting up documentation structure for a new project
  - Asked to "document this", "write the spec", "create the test doc", "update the docs", "audit the docs"
  - Making any code change — every code change requires a documentation change
  - Transferring technical knowledge to a business or strategic audience

  This skill defines the rules, structure, templates, and quality standards for all project documentation. If you are touching any documentation file — read this first.
---

# Software Architect-Writer

Every code change must be accompanied by the corresponding documentation update, committed in the same PR. No exceptions. Documentation scope follows change scope.

**Example — small change:** A behavior change in feature X → Update `x-spec.md`. Update `api-list.md` if any endpoint signature changed.

**Example — new feature:** → Create the feature folder with `feature-spec.md`, `feature-test.md`, and a diagram. Update `doc-map.md`. Update `api-list.md` if applicable.

**Example — architectural change:** → Update all affected feature specs. Revise `architecture-overview.md` and `architecture-decision-record.md`. Update `doc-map.md` if the folder structure changed.

## Protected Documents

Never modify these files unless explicitly instructed by the user. They represent either foundational conventions or the user's original intent — changing them requires a deliberate human decision:

- `standard-work-procedures.md`
- All files inside `/devops/`
- All files inside `/planning-specifications/` — these capture the user's original intent and must never be altered by an agent

## Documentation Rules

- **Be concise.** As short as possible while staying complete. Every sentence must earn its place. No padding, no repetition.
- **Be structured, not over-formatted.** Use clear headings. Avoid decorative bullets or deep nesting where prose would be clearer.
- **Be complete.** A document must contain all information needed to understand, build, and test the feature — no implicit knowledge assumed.
- **Be current.** Every document must have a `Last Updated: YYYY-MM-DD` date at the top. Update it whenever the file changes.
- **Write in English.** All documentation must be in English, regardless of the team's native language.
- **Ship docs with code.** Documentation changes must be in the same PR as the code. A PR with code changes and no doc update is incomplete.
- **Use consistent naming.** `feature-name-spec.md`, `feature-name-test.md`. Diagrams: `feature-name-diagram.png` or `.mermaid`.
- **Use diagrams where useful.** Complex flows, architecture, and data models must have a visual. Use Mermaid.js inline or PNG in the feature folder.
- **Never assume context.** Write as if the reader has not seen the code. The doc must be self-sufficient.

## Commands

### /plan
Create a `planning-specification` document from the user's description. Used before any code is written. Save to `/planning-specifications/YYYY-MM-DD-feature-name-plan.md`. This file must not be read or edited during development unless explicitly instructed — it is the source of truth for original intent. **Do not add planning specs to `doc-map.md`** — they are not part of the project documentation.

### /start
Create the initial documentation structure (folders and files) for a new feature or project, following the Project Structure below. **Adapt the structure to the actual project** — the structure is a pattern, not a prescription. Use the real feature names, include only the folders and files the project actually needs, and skip anything irrelevant. Create all files with placeholder content and `Last Updated` dates.

### /audit
Scan all documentation against the current codebase. For each doc, run two checks:

1. **Date check** — compare the spec's `Last Updated` date against the most recent code change in the corresponding feature. Flag any doc whose date is behind.
2. **Content check** — read the doc and the current code to verify the content is actually accurate. A doc whose date was bumped without reflecting real changes is still stale. Check that behaviors, component names, table schemas, env vars, and error codes documented in the spec match what the code actually does.

Produce an audit report listing every stale or inaccurate doc, what specifically changed or mismatches, and what needs to be corrected. The content check is the more important of the two — a correct date proves nothing if the content is wrong.

### /sketch
Create a brainstorm document exploring the desired feature: what is typically implemented, edge cases to consider, and additions worth suggesting beyond what was explicitly requested. Save to `/planning-specifications/YYYY-MM-DD-feature-name-sketch.md`.

### /standard
Create or update `standard-work-procedures.md` from the user's description of how their team works. The user provides their conventions — branching strategy, coding standards, commit format, deployment process, Definition of Done, etc. — and the agent writes a clean, structured document following the template in `references/standard-work-procedures-template.md`.

- If the file does not exist yet, create it from scratch using the template as a base, filling in the user's specifics.
- If the file already exists, update only the sections the user specifies. Leave everything else intact.
- This file is protected after creation — only update it via `/standard` or explicit user instruction.
- Be specific. Avoid vague rules like "write clean code." Every procedure should be concrete enough to follow without interpretation.

### /doc2notion
Reads the project's technical documentation and creates or updates a business-facing project page in Notion, so the strategic team and decision makers can track development without reading code.

**Reads from:** all feature specs and test docs in `/docs/features/`, `changelog.md`, `architecture-overview.md`.

**Audience:** non-technical stakeholders (CEO, PM, investors). Write in plain language. Focus on outcomes and status. No file paths, no component names, no jargon.

**Creates or updates a Notion project page with three subpages:**

1. **Overview & Features** — Business-friendly summary of what the project does and each feature it delivers. For each feature: what it is, what problem it solves, and its current status (🟢 Live / 🟡 In Development / ⚪ Planned). No implementation details.

2. **Feature Test List** — Checkbox list of every feature and its test cases, showing tested / not tested status per item. Derived directly from the `feature-test.md` files (Coverage Summary table). Updated on every run so stakeholders can track QA progress at a glance.

3. **Backlog** — Lists implemented and to-implement features. **Critical rule:** to-implement items already present in this subpage must never be modified, reordered, or removed — they are the business's planned roadmap and belong to the user. The agent only updates the status of features now confirmed as implemented. Never add new to-implement items unilaterally.

**Operational rules:**
- If the project page does not exist in Notion yet, create it. If it does, update the three subpages in place — do not recreate them from scratch or you will lose user-added content (especially in Backlog).
- Always reflect the actual current state of the docs. Never invent a status or feature not present in the codebase documentation.

## Project Documentation Structure

This is a **pattern**, not a literal template. Names are placeholders. The actual structure must reflect the real project — its features, its stack, its needs. Do not copy this verbatim; adapt it.

```
/project-folder
    /planning-specifications        # NOT mapped in doc-map.md
        YYYY-MM-DD-plan1-spec.md
        YYYY-MM-DD-plan2-spec.md
        {...}
    /docs
        doc-map.md                  # Index: what exists and where to find it
        standard-work-procedures.md # Project conventions and processes
        changelog.md                # Modification history — updated on git commit only
        /architecture               # System-wide view
            architecture-overview.md
            architecture-diagram.png
            architecture-decision-record.md
        /features
            /01-feature1
                feature1-spec.md
                feature1-test.md
                feature1-diagram.png  # if needed
            /02-feature2
                feature2-spec.md
                feature2-test.md
            {...}
            /N-featureN
                featureN-spec.md
                featureN-test.md
        /devops
            infrastructure-map.md
            env-variables.md
            runbooks-disaster.md
            {...}                   # other devops docs as needed
        /standards
            coding-standards.md
            git-flow.md
            {...}
        /qa-testing
            test-plan.md
            acceptance-criteria.md
            {...}
        /api
            api-list.md
    README.md
```

`doc-map.md` and `standard-work-procedures.md` are the two most critical files. `doc-map.md` must be updated whenever a new file or folder is added — but **never include `/planning-specifications` in it**.

## README.md

Lives at the project root. The "welcome" document — the first thing anyone reads when they encounter the project. Contains: a brief project summary, prerequisites, setup instructions, how to run locally, environment variables overview, and a pointer to `/docs` for deeper documentation. Keep it short and focused on getting someone from zero to running — it is not a feature spec.

## Changelog

`changelog.md` lives at `/docs/changelog.md` and records the project's modification history. It exists so commits and release notes can be written from a single, reliable source.

**Rules:**
- **Only update on `git commit`** — never during active development or mid-PR.
- **Keep entries concise** — one line per change. A reader should understand the full change set in under 30 seconds.
- **Group by category:** `Added`, `Changed`, `Fixed`, `Deprecated`. Skip categories with no entries.
- **Follow semantic versioning:** `[MAJOR.MINOR.PATCH] - YYYY-MM-DD`.
- **Do not log internal refactors or doc-only changes** unless they affect behavior visible to users or other systems.

**Format:**
```markdown
# Changelog

## [1.2.0] - YYYY-MM-DD
### Added
- **FeatureName:** One-line description of what was added.

### Changed
- **FeatureName:** What changed and why, in one line.

### Fixed
- **FeatureName:** What bug was fixed.

### Deprecated
- **EndpointName:** Why it is deprecated and when it will be removed.
```

## Reference Files

### Feature Spec → `references/feature-spec-template.md`
Template + worked example (`auth-spec.md`) for feature specs. Every spec must cover: Overview, Behavior (per action: input/output/side-effects/errors), Business Rules, Edge Cases, Implementation Map, Configuration, Dependencies, Diagrams. Goal: give an agent complete understanding of the feature without reading source code.

### Test Document → `references/test-doc-template.md`
Template + worked example (`auth-test.md`) for test docs. Every test doc must cover: a Coverage Summary table (all areas + status at a glance) and individual Test Cases with IDs, type (Unit/Integration/E2E), precise inputs, and exact expected outcomes.

### doc-map.md → `references/doc-map-template.md`
Template + worked example for the documentation index. Use when creating or heavily updating `doc-map.md`. Every file in `/docs` must appear here; planning specs must not.

### standard-work-procedures.md → `references/standard-work-procedures-template.md`
Template + worked example for the team's standard procedures document. Use when running `/standard`. Sections: development lifecycle, branching strategy, commit format, code review standards, testing & QA, documentation obligations, deployment process, Definition of Done.
