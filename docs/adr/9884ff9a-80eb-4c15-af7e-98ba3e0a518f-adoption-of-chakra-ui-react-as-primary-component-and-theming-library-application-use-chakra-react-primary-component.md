---
version: 2
name: 9884ff9a-80eb-4c15-af7e-98ba3e0a518f-adoption-of-chakra-ui-react-as-primary-component-and-theming-library-application-use-chakra-react-primary-component
description: "Adoption of @chakra-ui/react as Primary Component and Theming Library: Application Use Chakra React Primary Component"
metadata:
  actual:
    adr_id: 9884ff9a-80eb-4c15-af7e-98ba3e0a518f
    status: accepted
    candidate_group_id: 8ec0eb73-bba3-4664-ab02-5c257c095e8a
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - react-plugin-tools/react_plugin_template/src/**
---

# Adoption of @chakra-ui/react as Primary Component and Theming Library: Application Use Chakra React Primary Component

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `react-plugin-tools/react_plugin_template/src/**`

## Context

- The application requires a consistent design system, accessible UI primitives, and centralized theme configuration across page components and entry points
- Multiple presentation modules and application setup files consistently import and utilize `@chakra-ui/react` alongside theme definitions and color mode contexts
- Unifying on an established React component library avoids fragmented ad-hoc styling and accessibility defects across pages

## Problem Statement

Building custom accessible UI components and design systems from scratch introduces visual inconsistencies, increased engineering maintenance, and accessibility compliance gaps across page views.

## Decision

- **R-LM-001** MUST: The application MUST use `@chakra-ui/react` as the primary component library for presentation components, layout structures, and UI primitives.

## Policy Block

In scope:

- All React presentation components, pages, layout containers, and root application entry points
- Design token definitions, color mode contexts, and styling configurations

Out of scope:

- Non-visual business logic, data access clients, and backend service integration modules
- Static documentation assets and build orchestration configuration

## Rationale

- Static code analysis reveals consistent adoption of `@chakra-ui/react` across the presentation layer and theme configuration modules
- Using a single cohesive component library standardizes accessibility primitives, responsive styling props, and color mode handling across the application codebase
- Centralizing theme definitions ensures predictable visual hierarchies and reduces redundant custom CSS across components

## Consequences

Positive:

- Standardizes UI component primitives and theme customization across all presentation views
- Ensures accessibility primitives and design token hierarchies are consistently enforced across components
- Accelerates UI delivery by providing pre-built responsive layout and form components

Negative:

- Couples presentation layer components to proprietary styling APIs and component props of the adopted library
- Adds external bundle size overhead compared to raw CSS or headless alternatives
- Requires ongoing dependency maintenance and lockfile synchronization to prevent runtime styling regressions

## Alternatives

- Ad-hoc CSS or unconstrained utility styling without a unified component library (rejected)
  Rejected because: Increases duplication, prevents centralized theming, and fails to provide accessible component primitives out of the box
  When valid: Only viable for minimal standalone micro-applications with strict zero-dependency bundle budgets
- Adopting a completely unstyled headless UI primitive library requiring custom CSS design systems (rejected)
  Rejected because: Requires substantial upfront design token engineering and ongoing maintenance overhead
  When valid: Appropriate when the project requires bespoke design tokens and strict visual isolation from third-party styling paradigms

## Risks

- Breaking API or theme configuration changes between major library releases
  Mitigation: Enforce strict adherence to the lockfile grounding policy and evaluate release notes before dependency upgrades
  Owner: engineering team
- Client-side bundle bloat caused by unused component primitives
  Mitigation: Utilize tree-shaking and avoid unnecessary full-bundle imports across non-UI modules
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
- Centralize all custom design tokens, color modes, and component overrides in a single theme module passed to the root provider
- Wrap application entry points with the designated root provider and color mode context to ensure theme propagation to all page components

## Continuation Context

Verify commands:

- Discover and run the project static analysis and linting scripts to verify compliance with UI library import standards
- Discover and run the project type check script to ensure all component props and theme definitions align with resolved dependency types

Accept when:

- Verification commands succeed with zero errors reported by type checking and linting systems
- All UI components render using the adopted design system tokens and provider context

## Enforcement

- Verified by: Automated static analysis and type checking in the continuous integration pipeline
- Verified by: Peer code review of presentation components and styling imports
- Violation handling: Pull requests introducing unapproved styling mechanisms or bypassing the designated UI library are blocked pending remediation
- Violation handling: Violations flagged during static analysis must be refactored to use standard component primitives and tokens
- Exception process: Developers requesting an alternative UI component or custom styling approach outside the adopted component library must submit an architectural exception documenting necessity, bundle impact, and accessibility compliance
