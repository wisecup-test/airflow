---
version: 2
name: 8f886ca6-52b7-405d-bb8f-a824810d7235-adoption-of-react-icons-fi-for-user-interface-iconography-components-requiring-icons-unavailable-within-primary
description: "Adoption of react-icons/fi for User Interface Iconography: Components Requiring Icons Unavailable Within Primary"
metadata:
  actual:
    adr_id: 8f886ca6-52b7-405d-bb8f-a824810d7235
    status: accepted
    candidate_group_id: 15a2a578-19d9-42ce-a6d7-350b5449995e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/ui/src/**
---

# Adoption of react-icons/fi for User Interface Iconography: Components Requiring Icons Unavailable Within Primary

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/ui/src/**`

## Context

- The frontend presentation layer requires a cohesive visual language and standard icon set across navigation, action triggers, and status displays.
- Unconstrained icon imports risk fragmented visual styles through mixed iconography libraries or inline SVG markup.
- Direct submodule imports from react-icons/fi provide standardized visual elements while enabling module bundlers to isolate referenced symbols.

## Problem Statement

User interface components across navigation, dashboard panels, and control dialogs require consistent iconography without introducing bundle bloat from unbounded multi-vendor icon libraries or ad-hoc SVG paths.

## Decision

- **R-LM-001** SHOULD: Components requiring icons unavailable within the primary Feather icon set SHOULD use authorized peer subsets from react-icons via explicit subpath imports.

## Policy Block

In scope:

- User interface layout components requiring visual symbols or navigation cues.
- Action buttons, dropdown indicators, and status badges within frontend pages.

Out of scope:

- Backend services, data pipelines, and non-visual core engine modules.
- External plugins that bring their own isolated rendering pipelines.

## Rationale

- Standardizing on react-icons/fi establishes visual consistency across all buttons, navigation bars, and state indicators.
- Direct submodule importing ensures that only explicitly referenced icon components are bundled into the application distribution.
- Consolidating visual assets into a unified component library reduces interface design fragmentation and maintenance overhead across frontend modules.

## Consequences

Positive:

- Uniform visual aesthetics and iconography across navigation, state badges, and control dialogs.
- Optimized bundle size through submodule-level tree-shaking of icon components.
- Simplified developer workflow by providing a comprehensive, predictable icon library.

Negative:

- Frontend components are coupled to the visual design language and symbol taxonomy of the adopted icon library.
- Domain-specific icons not present in the primary collection require secondary submodule imports or design adaptations.

## Alternatives

- Authoring custom inline SVG elements directly within component files (rejected)
  Rejected because: Custom SVG markup leads to visual inconsistency, code duplication, and increased component maintenance costs.
  When valid: Valid only for unique trademarked brand marks or proprietary domain graphics unavailable in any standard icon library.
- Importing from root react-icons bundle entry points without submodule paths (rejected)
  Rejected because: Root imports risk defeating tree-shaking mechanisms and inflating client asset bundle sizes.
  When valid: Valid only in environments where build tooling strictly eliminates unused root re-exports automatically.

## Risks

- Missing specialized glyphs or domain concepts within the primary icon set requiring alternative icon sets.
  Mitigation: Permit secondary scoped submodule imports from the same parent icon library family when necessary.
  Owner: Frontend engineering team
- Accidental root package imports bypassing submodule path conventions.
  Mitigation: Configure static analysis import linting rules to enforce subpath import restrictions.
  Owner: Frontend engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Import icon components using explicit submodule specifiers rather than root package imports to preserve modularity.
- Ensure accessible label properties or screen-reader text are attached to icon-only interactive controls.

## Continuation Context

Verify commands:

- Discover and run the repository linter to verify that iconography imports conform to authorized submodule paths.
- Discover and execute the frontend type-checking and build validation commands to confirm icon symbol resolutions.

Accept when:

- Repository static analysis passes with zero import violations regarding iconography module paths.
- Frontend compilation completes successfully with all icon symbols resolved from authorized submodules.

## Enforcement

- Verified by: Automated static analysis checks in continuous integration pipelines.
- Verified by: Peer code review for visual consistency and proper subpath import structure.
- Violation handling: Pull requests containing disallowed root imports or unapproved inline SVG markup are blocked until corrected.
- Exception process: Exceptions for unique proprietary graphics or fallback icon subsets must be submitted via design review approval.
