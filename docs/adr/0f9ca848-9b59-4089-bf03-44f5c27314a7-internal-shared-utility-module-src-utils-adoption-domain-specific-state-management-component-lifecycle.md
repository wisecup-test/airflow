---
version: 2
name: 0f9ca848-9b59-4089-bf03-44f5c27314a7-internal-shared-utility-module-src-utils-adoption-domain-specific-state-management-component-lifecycle
description: "Internal Shared Utility Module (src/utils) Adoption: Domain Specific State Management Component Lifecycle"
metadata:
  actual:
    adr_id: 0f9ca848-9b59-4089-bf03-44f5c27314a7
    status: accepted
    candidate_group_id: 22af00d2-2255-44e9-bdfe-be1f7a4dc708
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/ui/src/**
---

# Internal Shared Utility Module (src/utils) Adoption: Domain Specific State Management Component Lifecycle

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/ui/src/**`

## Context

- Frontend presentation components, page views, and asynchronous query hooks require common helper functions for data transformation, formatting, and parameter processing.
- Without a designated internal module, cross-cutting utility functions tend to be duplicated across presentation and data-fetching boundaries or embedded inconsistently within component logic.
- Static analysis across the frontend codebase reveals widespread imports from the internal src/utils module alongside UI primitives and generated API clients.

## Problem Statement

Frontend features spanning presentation components, page-level filter panels, and asynchronous query hooks require recurring utility operations such as data formatting and parameter handling. In the absence of an architectural constraint governing utility consolidation, developers either reinvent identical helper routines within isolated components or scatter ad-hoc utility files across directory trees, leading to inconsistent runtime behavior, code duplication, and tight coupling between disparate presentation modules.

## Decision

- **R-LM-001** MUST_NOT: Domain-specific state management and component lifecycle side effects MUST NOT be encapsulated inside the shared utility module.

## Policy Block

In scope:

- All frontend components, page views, and asynchronous query hooks requiring common data manipulation, formatting, and cross-cutting helpers.

Out of scope:

- Component-specific layout markup and view-rendering logic that is not reusable across modules.
- Generated API client bindings and contract definitions produced by automated schema generators.

## Rationale

- Centralizing common logic in src/utils establishes a single source of truth for cross-cutting transformations across presentation components and data queries, preventing divergence.
- The architectural boundary prevents code duplication across multiple pages and query hooks, as evidenced by consistent imports across components, filters, and mutation handlers.
- Maintaining pure, presentation-agnostic helper functions in an internal utility module isolates business transformations from UI rendering frameworks and simplifies unit testing.

## Consequences

Positive:

- Eliminates redundant helper logic and divergent formatting across UI components, page filters, and query hooks.
- Simplifies unit testing by isolating pure utility functions from presentation frameworks and DOM lifecycles.
- Provides a clear and consistent module import location for common functionality across the frontend workspace.

Negative:

- Creates a centralized module that risks accumulating unrelated helper functions if domain boundaries are not actively maintained.
- Increases the blast radius of utility modifications, where changes to core helper functions can affect multiple disparate UI views and query hooks.

## Alternatives

- Ad-hoc local utility definitions within individual component directories (rejected)
  Rejected because: Leads to widespread duplication of identical formatting and transformation functions, inconsistent behavior across pages, and higher maintenance overhead.
  When valid: Valid only when a helper function is strictly tied to a single component and has zero applicability elsewhere.
- Embedding data transformations directly inside asynchronous query hooks and page components (rejected)
  Rejected because: Tightly couples data manipulation logic with component lifecycle and network query orchestration, making pure unit testing difficult.
  When valid: Valid only when transformation logic is purely ephemeral and bound directly to a single component render tree.

## Risks

- The shared utility module becomes a catch-all dumping ground for loosely related functions without clear domain boundaries.
  Mitigation: Enforce module cohesion during code review and partition large utility scopes into coherent domain-specific utility submodules.
  Owner: Frontend engineering team
- Breaking changes in core utilities unintentionally break consuming components or query hooks across the application.
  Mitigation: Require high unit test coverage on all shared utility exports and run automated workspace test suites during continuous integration.
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
- Discover the workspace module resolution configuration to understand path alias mappings and module resolution rules for internal imports.
- Ensure helper functions exported by the utility module remain pure and free from hidden state or component lifecycle dependencies.

## Continuation Context

Verify commands:

- Discover and run the project's static analysis and linting scripts to verify import path compliance and detect circular dependencies.
- Discover and execute the unit testing suite to confirm that all shared utility functions pass their test specifications.
- Discover and invoke the type-checking verification script to validate type signatures across utility exports and consumers.

Accept when:

- Static analysis and lint verification pass with zero unresolved module import errors.
- All unit tests covering shared utility functions pass successfully without regressions.
- Type checking passes across all consumers importing from the internal utility module.

## Enforcement

- Verified by: Automated continuous integration checks running linting, type validation, and unit test suites.
- Verified by: Peer code review verifying that reusable helpers are placed in the shared utility module rather than duplicated locally.
- Violation handling: Pull requests containing duplicated utility logic or violating unidirectional dependency boundaries are blocked until refactored.
- Exception process: Exceptions for one-off component-specific helpers must be documented with architectural rationale and approved by frontend code owners.
