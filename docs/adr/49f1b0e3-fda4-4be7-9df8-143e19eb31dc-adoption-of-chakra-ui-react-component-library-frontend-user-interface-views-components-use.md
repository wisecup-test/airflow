---
version: 2
name: 49f1b0e3-fda4-4be7-9df8-143e19eb31dc-adoption-of-chakra-ui-react-component-library-frontend-user-interface-views-components-use
description: "Adoption of @chakra-ui/react Component Library: Frontend User Interface Views Components Use"
metadata:
  actual:
    adr_id: 49f1b0e3-fda4-4be7-9df8-143e19eb31dc
    status: accepted
    candidate_group_id: a268c66b-0711-41d3-b4d1-505a32c6b152
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of @chakra-ui/react Component Library: Frontend User Interface Views Components Use

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Frontend user interface modules require standardized, accessible, and themeable UI components across administrative views, authentication forms, and operational monitoring dashboards.
- Independent development across multiple frontend screens risks inconsistent visual styling, accessibility gaps, and duplicate component implementations without a shared component library.
- Static analysis demonstrates pervasive adoption of @chakra-ui/react across 109 interface files as the shared component system.

## Problem Statement

Frontend applications requiring consistent visual design, robust accessibility, and rapid layout composition need a unified component system to prevent fragmented UI styles, duplicated accessibility logic, and divergent user interface implementations across different application views.

## Decision

- **R-LM-001** MUST: Frontend user interface views and components MUST use @chakra-ui/react as the primary component library for visual primitives, dialogs, form elements, and layout containers.

## Policy Block

In scope:

- All frontend user interface modules, components, forms, modals, and operational views within the project.

Out of scope:

- Backend API services, command-line utilities, server-side data models, and non-browser runtime logic.

## Rationale

- The presence of @chakra-ui/react across 109 frontend files demonstrates established consensus on a single design system and component abstraction layer.
- Using a unified component library provides standardized accessibility patterns, predictable layout primitives, and cohesive theming across distributed interface components.
- Consolidating UI primitives on a single library avoids fragmentation and minimizes the cognitive load for frontend contributors.

## Consequences

Positive:

- Standardizes component presentation, layout structures, and accessibility primitives across user-facing modules.
- Accelerates frontend feature delivery by providing tested building blocks for inputs, modals, cards, and data displays.
- Unifies styling abstractions and theme token management across the frontend codebase.

Negative:

- Couples the application presentation layer to the component library design tokens, layout primitives, and release lifecycle.
- Upgrading major library versions requires cross-cutting audits of component props and theme definitions across all consuming modules.
- Increases client bundle size relative to purely unstyled custom markup.

## Alternatives

- Custom unmanaged CSS styling without a shared component library (rejected)
  Rejected because: Increases maintenance overhead, produces visual inconsistencies, and requires manual implementation of complex accessibility primitives.
  When valid: Valid only for isolated standalone micro-widgets that cannot tolerate external library dependencies.
- Adopting multiple disparate third-party UI component libraries concurrently across different frontend modules (rejected)
  Rejected because: Bloats client bundle size, introduces conflicting design tokens and theming providers, and degrades developer experience.
  When valid: Valid during an active migration window with an explicit deprecation timeline.

## Risks

- Breaking API modifications between major versions of the component library could require extensive refactoring across consuming modules.
  Mitigation: Enforce lock-file version grounding and verify API contracts against exact resolved version documentation before upgrading.
  Owner: engineering team
- Excessive client bundle growth from importing non-essential component submodules.
  Mitigation: Monitor production client bundle metrics and configure module-level tree-shaking to eliminate unused component code.
  Owner: engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Compose component primitives hierarchically into domain-specific compound widgets rather than duplicating core structural markup.
- Ensure all interactive component instances comply with accessibility standards supported by the underlying component primitives.

## Continuation Context

Verify commands:

- Discover and run the project static analysis and linting command declared in project configuration scripts.
- Discover and execute the frontend build command defined in the dependency manifest to verify component compilation.
- Discover and run the frontend automated test suite to ensure component rendering and integration integrity.

Accept when:

- All frontend components import UI primitives according to design system guidelines and pass static type verification.
- The frontend build completes successfully with zero compilation or style resolution errors.
- Frontend test suites execute cleanly with all component rendering and state interaction tests passing.

## Enforcement

- Verified by: Automated linting rules that inspect module imports in user interface files.
- Verified by: Continuous integration checks that validate frontend build integrity and type safety.
- Verified by: Peer code reviews on pull requests affecting user interface presentation layers.
- Violation handling: Pull requests introducing unapproved parallel component libraries or unvetted UI frameworks MUST be blocked by automated checks.
- Violation handling: Violations identified in review must be refactored to consume approved component primitives before merge approval.
- Exception process: Submit an architectural exception request detailing technical constraints preventing the use of standard component primitives.
- Exception process: Obtain approval from frontend architecture maintainers prior to merging bespoke component primitives.
- Exception process: Document accepted exceptions with an agreed timeline for aligning with the shared component library.
