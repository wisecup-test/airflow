---
version: 2
name: 8d0d81f0-52f5-40f3-820b-4d890ae615a4-fastapi-route-level-dependency-injection-for-internal-api-access-control-router-endpoint-registrations-declare-explicit-error
description: "FastAPI Route-Level Dependency Injection for Internal API Access Control: Router Endpoint Registrations Declare Explicit Error"
metadata:
  actual:
    adr_id: 8d0d81f0-52f5-40f3-820b-4d890ae615a4
    status: accepted
    candidate_group_id: f50db5b0-5a17-40f0-ba16-91c75d8a2bbb
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# FastAPI Route-Level Dependency Injection for Internal API Access Control: Router Endpoint Registrations Declare Explicit Error

Date: 2025-02-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Internal API routes require consistent enforcement of entity-level access control policies across varying operational contexts including UI backends, public routes, and execution engines.
- Scattering authorization checks and database access controls across endpoint function bodies leads to code duplication, inconsistent security enforcement, and difficult auditing.
- FastAPI provides a declarative dependency injection mechanism that allows access guards and request filtering logic to be attached directly to route decorator definitions.
- Standardizing access control declarations at the router boundary guarantees that access policies are evaluated before route handler execution begins.

## Problem Statement

Internal API endpoints risk inconsistent security enforcement and redundant boilerplate when authorization logic is implemented imperatively inside individual route functions. Without declarative route-level boundary controls, endpoint handlers often drift in how they validate resource access, manage error responses, and expose internal schemas to external or client consumers.

## Decision

- **R-IA-001** SHOULD: Router endpoint registrations SHOULD declare explicit error response schemas using standardized HTTP exception documentation helpers in the route responses parameter.

## Policy Block

In scope:

- HTTP route definitions within internal API routers and execution API routers.
- Internal API endpoints exposing access to domain entities, task instances, dag runs, and runtime execution state.

Out of scope:

- Direct internal function calls across service boundaries that do not traverse HTTP router interfaces.
- Low-level serialization or data mapping utilities that operate without network or authentication context.

## Rationale

- Declarative dependency injection in FastAPI route decorators separates cross-cutting authorization concerns from endpoint business logic.
- Centralizing resource access entity guards through Depends ensures consistent evaluation order and standardized error responses across internal endpoints.
- Route-level declarations automatically propagate to OpenAPI documentation schemas, providing accurate client boundary contracts without manual synchronization.

## Consequences

Positive:

- Uniform security policy enforcement across all internal API endpoints prior to route body execution.
- Reduced boilerplate and eliminated duplicate access checking across individual endpoint handlers.
- Automatic synchronization of authorization and error response contracts with generated OpenAPI schemas.

Negative:

- Tighter architectural coupling of endpoint routing modules to framework dependency injection primitives.
- Steeper learning curve for developers needing to compose complex multi-resource authorization guards via dependency factories.
- Debugging authorization failures requires tracing through dependency resolution chains rather than imperative function code.

## Alternatives

- Imperative authorization checks inside route handler function bodies (rejected)
  Rejected because: Leads to code duplication, inconsistent validation order, and failure to reflect authorization requirements in OpenAPI schemas.
  When valid: Valid only in standalone script utilities lacking framework dependency injection infrastructure.
- Global middleware-based URL pattern matching for authorization (rejected)
  Rejected because: Lacks the granular entity-level parameter context required to authorize access to specific sub-resources.
  When valid: Valid for coarse-grained edge gateway authentication or network IP allowlisting.

## Risks

- Developers might inadvertently omit required access guard dependencies on newly created router endpoints.
  Mitigation: Implement automated linting or static test suites that verify all registered router endpoints declare required access dependencies.
  Owner: engineering team
- Overly complex dependency graphs can increase endpoint latency due to repeated sub-dependency evaluation.
  Mitigation: Ensure access guard factories use cached or scoped dependency resolution where appropriate.
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
- When constructing route definitions, pass authorization guard factories directly to the dependencies parameter of the HTTP method decorator.
- Combine route-level dependencies with standardized response schema helpers in the responses parameter to document standard HTTP status codes and error payloads.

## Continuation Context

Verify commands:

- Discover and run the project's static analysis and linting suite to ensure all internal API route definitions declare required authorization dependencies.
- Discover and execute the API test suite to verify that unauthenticated or unauthorized requests to protected endpoints return expected HTTP status codes.

Accept when:

- Static verification confirms that every registered internal API endpoint decorator contains the necessary route dependency declarations.
- Automated API route tests verify that endpoints reject requests failing access guard evaluation with standardized error responses.

## Enforcement

- Verified by: Automated route verification tests integrated into the continuous integration pipeline.
- Verified by: Static analysis checks validating router decorator parameters across all API route modules.
- Verified by: Peer code review for all new or updated internal API endpoint definitions.
- Violation handling: Pull requests missing required authorization dependencies or standardized error schemas will be blocked by automated test failures and review rejection.
- Violation handling: Discovered un-guarded internal endpoints must be remediated immediately via high-priority bugfix pull requests.
- Exception process: Exceptions for unprotected internal endpoints must be reviewed and approved by the API architecture team and documented with explicit security justifications.
