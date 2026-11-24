---
version: 2
name: 520ee480-079e-49f1-93e1-6831a19b931a-fastapi-framework-adoption-for-http-api-routing-and-dependency-injection-route-handlers-not-instantiate-database-sessions
description: "FastAPI Framework Adoption for HTTP API Routing and Dependency Injection: Route Handlers Not Instantiate Database Sessions"
metadata:
  actual:
    adr_id: 520ee480-079e-49f1-93e1-6831a19b931a
    status: accepted
    candidate_group_id: b7343a4c-5ee2-48c1-99aa-91db6460b471
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/**
---

# FastAPI Framework Adoption for HTTP API Routing and Dependency Injection: Route Handlers Not Instantiate Database Sessions

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/api_fastapi/**`

## Context

- The application exposes multiple HTTP service boundaries including public APIs, internal user-interface APIs, and worker execution APIs.
- Handling cross-cutting concerns such as access authorization, query parameter parsing, and database session lifecycles imperatively creates boilerplate and inconsistency across endpoints.
- Eighty route and service modules have adopted the FastAPI framework and its declarative dependency injection mechanisms to structure endpoint routing and contract definitions.

## Problem Statement

Inconsistent endpoint definitions and manual handling of authentication, authorization, query parsing, and session lifecycles across diverse HTTP API surfaces lead to security regressions, redundant code, and inaccurate API schema documentation.

## Decision

- **R-LM-001** MUST_NOT: Route handlers MUST_NOT instantiate database sessions directly or execute business logic without declaring corresponding Depends dependencies on route definitions or path operation decorators.

## Policy Block

In scope:

- HTTP API endpoint declarations across public, user-interface, and internal execution service layers.
- Route dependency declarations for authorization, authentication, query parameter parsing, and database session injection.

Out of scope:

- Asynchronous task execution worker processes that do not expose HTTP interfaces.
- Low-level database migrations and offline administrative batch routines.

## Rationale

- Adopting FastAPI standardizes HTTP routing across disparate subsystems into a cohesive, declarative structure.
- The Depends dependency injection mechanism ensures access control checks and resource lifecycles are consistently enforced prior to route handler execution.
- Empirical evidence from eighty routing modules demonstrates established consensus and structural alignment around this framework pattern.

## Consequences

Positive:

- Establishes a uniform, declarative pattern for route definition, request validation, and response serialization across all API surfaces.
- Centralizes access control and session management via composable dependency injection rather than repetitive boilerplate.
- Enables automated OpenAPI schema generation directly from route signatures and parameter models.

Negative:

- Couples route handlers tightly to framework-specific dependency injection semantics and lifecycle handling.
- Requires learning framework-specific dependency override patterns for testing isolated endpoint behaviors.
- Deep dependency trees in route signatures can introduce overhead if provider hierarchies become excessively complex.

## Alternatives

- Legacy synchronous web framework with manual request context handling (rejected)
  Rejected because: Lacks native declarative dependency injection, type-driven schema validation, and unified async path support.
  When valid: Valid only in legacy subsystems where migration cost outweighs maintenance overhead.
- Imperative endpoint middleware interceptors for authentication and parameter validation (rejected)
  Rejected because: Obscures route-level access control contracts and bypasses modular schema synthesis.
  When valid: Valid for network-level transport transformations that apply universally regardless of route semantics.

## Risks

- Deep coupling between endpoint definitions and framework-specific dependency injection signatures.
  Mitigation: Encapsulate domain service operations into reusable provider callables that abstract internal service mechanics from route controllers.
  Owner: API architecture team
- Accidental omission of authorization dependencies on new route endpoints.
  Mitigation: Enforce automated static route inspection in integration pipelines and apply base dependencies to parent router hierarchies.
  Owner: Security engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Organize endpoint routers hierarchically by domain boundary, attaching common security and telemetry dependencies at the router level via router inclusion methods.
- Ensure all dependency providers yield or return typed domain objects to facilitate static contract validation and unit test dependency overriding.

## Continuation Context

Verify commands:

- Discover and run the project static analysis suite to verify that all API endpoint route definitions conform to framework dependency conventions.
- Discover and execute the test runner to validate that endpoint authorization and dependency injection bindings execute successfully across route suites.
- Discover and execute the project schema generation script to verify OpenAPI contract generation from registered route handlers.

Accept when:

- Static analysis checks pass with zero violations of route dependency declarations.
- All API endpoint tests pass, confirming correct resolution of dependency injection providers.
- API specification generation completes successfully without schema definition errors.

## Enforcement

- Verified by: Automated continuous integration linting and type-checking suites.
- Verified by: Peer code review verification of dependency injection usage on all route endpoints.
- Verified by: Contract verification tests comparing generated API schemas against endpoint definitions.
- Violation handling: Pull requests introducing route handlers without declarative dependency injection are blocked at automated review.
- Violation handling: Endpoints missing authorization dependencies trigger automated test failures during pipeline execution.
- Exception process: Submit an architectural review request to the API architecture team documenting why framework-provided dependency injection cannot satisfy the integration requirement.
- Exception process: Exceptions must define alternative security validation controls approved by security engineering before merging.
