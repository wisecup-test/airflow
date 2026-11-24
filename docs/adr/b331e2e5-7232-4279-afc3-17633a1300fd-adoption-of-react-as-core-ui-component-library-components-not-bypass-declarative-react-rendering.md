---
version: 2
name: b331e2e5-7232-4279-afc3-17633a1300fd-adoption-of-react-as-core-ui-component-library-components-not-bypass-declarative-react-rendering
description: "Adoption of React as Core UI Component Library: Components Not Bypass Declarative React Rendering"
metadata:
  actual:
    adr_id: b331e2e5-7232-4279-afc3-17633a1300fd
    status: accepted
    candidate_group_id: b3d915c9-1a3c-4fd4-87f0-bbf1746e2dbb
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - react-plugin-tools/react_plugin_template/src/**
---

# Adoption of React as Core UI Component Library: Components Not Bypass Declarative React Rendering

Date: 2025-05-15
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `react-plugin-tools/react_plugin_template/src/**`

## Context

- The project template requires a standardized, declarative model for defining and rendering user interface components across application views.
- Intermediate representation evidence confirms core library dependencies on React and companion client rendering modules across multiple entrypoint files.
- Establishing a governed UI library standard ensures uniform component lifecycles, contextual state propagation, and consistent rendering across plugin modules.

## Problem Statement

Without an explicit architectural decision designating React as the core UI library, disparate components risk adopting conflicting rendering abstractions or uncoordinated imperative DOM mutations, undermining consistency and maintainability across plugin modules.

## Decision

- **R-LM-001** MUST_NOT: Components MUST NOT bypass the declarative React rendering pipeline with uncoordinated direct DOM updates.

## Policy Block

In scope:

- Client-side user interface components, pages, and entrypoints within the plugin template.

Out of scope:

- Non-UI utility modules, backend services, or build-time script automation.

## Rationale

- Adopting React establishes a standardized, component-driven architecture that facilitates declarative UI composition and consistent state sharing.
- Evidence across both main and development entrypoints demonstrates an existing commitment to React and client rendering abstractions.
- Standardizing on React aligns plugin template views with modern component paradigms, ensuring modularity and maintainable component hierarchies.

## Consequences

Positive:

- Provides a unified declarative component model across application views and development entrypoints.
- Enables modular composition of contextual providers, theme configurations, and page hierarchies.
- Streamlines plugin template development through standardized client rendering abstractions.

Negative:

- Introduces architectural coupling to the React runtime and rendering lifecycle.
- Requires adherence to component rendering rules and state synchronization constraints.

## Alternatives

- Direct imperative Document Object Model manipulation without a component library (rejected)
  Rejected because: Direct DOM manipulation scales poorly across complex views, lacks declarative state synchronization, and increases defect surface area.
  When valid: Lightweight isolated scripts requiring zero external dependencies and trivial single-element DOM updates.
- Adopting an alternative component framework (rejected)
  Rejected because: Inconsistent with the existing codebase dependencies and plugin tooling template architecture established across the repository.
  When valid: Projects with distinct architectural constraints or non-browser rendering targets.

## Risks

- Breaking changes or deprecated APIs across major library updates
  Mitigation: Enforce strict dependency resolution via lock artifacts and verify public API compatibility against exact locked documentation.
  Owner: Frontend Engineering
- Performance degradation from unnecessary component re-rendering in complex component trees
  Mitigation: Structure state boundaries cleanly and use contextual isolation for frequently updated values.
  Owner: Frontend Engineering

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Isolate presentation components from context providers and routing logic to facilitate modular testing and template reuse.
- Maintain consistent client mounting patterns across standard application entrypoints and local development entrypoints.

## Continuation Context

Verify commands:

- Discover the repository test and type-checking scripts declared in the project configuration and execute them to verify that all React components compile and satisfy type constraints.
- Discover and execute the project build script to validate that component bundles render and package without errors.

Accept when:

- All user interface modules and client entrypoints compile without unresolved references or type errors.
- Client entrypoints mount root React component hierarchies successfully during project test and build execution.

## Enforcement

- Verified by: Automated type checking and linting rules enforced during continuous integration.
- Verified by: Peer code reviews of component structure and entrypoint mounting logic.
- Violation handling: Pull requests introducing unapproved rendering libraries or direct imperative DOM mutations will be blocked at continuous integration and code review.
- Exception process: Exceptions must be documented in an architectural review and approved by technical leadership prior to introducing alternative component rendering libraries.
