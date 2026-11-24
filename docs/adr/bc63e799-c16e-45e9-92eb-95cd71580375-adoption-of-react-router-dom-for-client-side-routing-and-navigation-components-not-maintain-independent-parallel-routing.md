---
version: 2
name: bc63e799-c16e-45e9-92eb-95cd71580375-adoption-of-react-router-dom-for-client-side-routing-and-navigation-components-not-maintain-independent-parallel-routing
description: "Adoption of react-router-dom for Client-Side Routing and Navigation: Components Not Maintain Independent Parallel Routing"
metadata:
  actual:
    adr_id: bc63e799-c16e-45e9-92eb-95cd71580375
    status: accepted
    candidate_group_id: 17b94952-60da-4ce9-a8e3-a0adda2dd2ef
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of react-router-dom for Client-Side Routing and Navigation: Components Not Maintain Independent Parallel Routing

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Client-side web applications require predictable navigation, view transitions, and deep-linking across distinct operational views.
- User interface views frequently depend on URL search parameters to preserve filtering, tab selection, and entity inspection across reloads.
- Static analysis across fifty-two interface files confirms uniform adoption of react-router-dom for route definition, navigation hooks, and search parameter access.

## Problem Statement

Decentralized client navigation and ad-hoc window location manipulation lead to inconsistent browser history, broken back/forward navigation, and fragile URL query synchronization across complex operational dashboards. A standardized client routing foundation is necessary to unify navigation lifecycles and URL state handling.

## Decision

- **R-LM-001** MUST_NOT: Components MUST NOT maintain independent or parallel routing mechanisms that bypass the centralized react-router-dom router context.

## Policy Block

In scope:

- User interface applications requiring client-side routing, view switching, and navigation.
- Components, custom hooks, and layout hierarchies that interact with route paths and search parameters.

Out of scope:

- Backend services, server-side route definitions, and non-browser execution environments.
- Static standalone HTML documentation assets that do not operate as single-page applications.

## Rationale

- Adopting react-router-dom unifies single-page application navigation and route handling under a single declarative framework supported by extensive community documentation.
- Query parameter access through standardized router hooks enables consistent deep-linking and state restoration across complex dashboard layouts.
- Enforcing a single routing dependency avoids divergent navigation abstractions across separate sections of the frontend interface.

## Consequences

Positive:

- Consistent navigation behavior and reliable browser history handling across all frontend modules.
- Preserved URL state and shareable deep links for filters, tabs, and entity inspection views.
- Simplified testing through reusable router test context wrappers.

Negative:

- Tight architectural coupling to react-router-dom lifecycle hooks and router component structures.
- Component tree dependencies requiring all navigational elements to reside within router provider contexts.
- Potential breaking changes across major router releases requiring coordinated interface updates.

## Alternatives

- Native browser History API and window location manipulation (rejected)
  Rejected because: Lacks declarative routing hierarchies, automated route matching, and seamless component state synchronization.
  When valid: In minimal standalone scripts or non-framework micro-widgets without nested view hierarchies.
- Alternative client routing libraries (rejected)
  Rejected because: Would introduce fragmented navigation idioms and redundant dependencies alongside the dominant react-router-dom adoption.
  When valid: When bundle size constraints preclude standard client router solutions in constrained environments.

## Risks

- Upstream breaking changes between major versions of the routing library affecting navigation hooks and route definition components.
  Mitigation: Verify resolved dependency versions against authoritative repository lock artifacts and encapsulate router access within domain-specific navigation hooks.
  Owner: Engineering Team
- Rendering components that use navigation primitives outside of a router context leads to runtime errors in isolation or unit testing.
  Mitigation: Provide standardized mock and provider test utilities wrapping components with router context during automated test execution.
  Owner: Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Encapsulate URL query parameter keys and retrieval logic inside dedicated custom hooks to avoid hardcoded parameter string literals across views.
- Ensure all unit and component integration tests import shared test wrapper utilities providing the appropriate routing harness.

## Continuation Context

Verify commands:

- Discover and run the project static analysis and linting suite to ensure all client navigation imports reference react-router-dom.
- Discover and execute frontend automated unit and component tests to confirm route matching and navigation behaviors pass.

Accept when:

- Static analysis validates all client routing and search parameter usages originate from react-router-dom without direct window location mutations.
- All frontend unit and integration test suites pass with full router context resolution.

## Enforcement

- Verified by: Automated continuous integration checks executing lint rules and type validation on pull requests.
- Verified by: Peer code review verifying adherence to centralized navigation hooks and search parameter patterns.
- Violation handling: Pull requests containing unapproved routing libraries or raw location manipulation are blocked until corrected.
- Exception process: Architectural review and exception approval documented in writing before introducing alternative routing mechanisms.
