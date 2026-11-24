---
version: 2
name: 81df26fc-9bc0-4496-be8a-99ba0266f3a2-fastapi-framework-adoption-for-http-services-and-api-routing-http-response-structures-error-contracts-explicitly
description: "FastAPI Framework Adoption for HTTP Services and API Routing: Http Response Structures Error Contracts Explicitly"
metadata:
  actual:
    adr_id: 81df26fc-9bc0-4496-be8a-99ba0266f3a2
    status: accepted
    candidate_group_id: b594e51f-ba94-4856-b35d-73a39b709566
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/**
---

# FastAPI Framework Adoption for HTTP Services and API Routing: Http Response Structures Error Contracts Explicitly

Date: 2025-02-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/api_fastapi/**`

## Context

- The service architecture requires a structured, declarative framework for building RESTful endpoints across public APIs, authentication boundaries, and internal execution endpoints.
- Previous API implementations required uniform schema validation, structured error responses, and automated OpenAPI documentation generation across all service modules.
- FastAPI was introduced to unify request routing, declarative authorization via dependency injection, and asynchronous request handling across seven distinct modules in the API layer.

## Problem Statement

Maintaining disparate HTTP routing structures, manual parameter validation, and inconsistent error response models across API subsystems leads to contract drift, authorization oversights, and increased maintenance overhead. The API layer requires a cohesive framework to enforce declarative schema contracts, centralized dependency injection for access controls, and standardized HTTP response handling.

## Decision

- **R-LM-001** MUST: HTTP response structures and error contracts MUST be explicitly documented using status code specifications and schema references in route decorator definitions.

## Policy Block

In scope:

- HTTP route definitions within public and execution API boundaries
- Authentication endpoints and session management middleware
- Health and diagnostic check endpoints

Out of scope:

- Core orchestration engine tasks and background daemon loops executing outside HTTP transport
- Database schema migrations and offline maintenance routines

## Rationale

- Static analysis across multiple API packages demonstrates widespread reliance on FastAPI routing abstractions, dependency injection, and structured response definitions.
- FastAPI provides built-in schema serialization and automated OpenAPI documentation, reducing redundant boilerplate while maintaining explicit contract validation.
- Dependency injection via Depends allows authorization, logging, and request context resolution to be declaratively composed at the route definition level without coupling handlers to infrastructure details.

## Consequences

Positive:

- Enforces declarative request and response validation aligned with automated OpenAPI schema documentation.
- Standardizes cross-cutting concerns like authorization and audit logging through composable dependency injection.
- Enables native asynchronous execution for input-output bound health checks and external client interactions.

Negative:

- Couples HTTP transport logic and route declarations directly to the framework abstractions and underlying ASGI toolkit.
- Increases architectural complexity in middleware dispatch order and asynchronous request context propagation.
- Requires disciplined lockfile version grounding to prevent API divergence across framework updates.

## Alternatives

- Legacy WSGI Microframework Architecture (rejected)
  Rejected because: Lacks native asynchronous execution models, built-in dependency injection, and automated OpenAPI documentation generation.
  When valid: Valid for legacy synchronous endpoints with minimal concurrency requirements.
- Custom In-House HTTP Routing and Dispatch Layer (rejected)
  Rejected because: Significantly increases long-term maintenance burden and reinvents schema validation, documentation, and middleware capabilities.
  When valid: Valid when protocol constraints prohibit standard HTTP frameworks.

## Risks

- Framework version drift or breaking changes between major releases impacting route decorators and dependency injection semantics.
  Mitigation: Adhere to the mandatory lockfile version grounding procedure and validate all route signatures against official versioned documentation.
  Owner: API Engineering Team
- Improper usage of synchronous blocking calls inside async route handlers leading to event loop starvation.
  Mitigation: Conduct code reviews and static verification checks to ensure blocking operations run in dedicated execution threads or use synchronous handler signatures.
  Owner: Backend Platform Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- When adding new route handlers, register them onto modular router instances and compose shared dependencies using the Depends mechanism rather than importing global state.
- Ensure all error conditions return structured HTTP exception responses with explicit status codes defined in the route response metadata.

## Continuation Context

Verify commands:

- Discover the project test runner and execute the automated API route and middleware test suite.
- Discover the project linter and type checker to verify route parameter annotations and dependency signatures.
- Execute the repository contract verification script to ensure generated OpenAPI schemas match declared models.

Accept when:

- All API endpoints return appropriate status codes and conform to declared response schemas across automated test executions.
- Dependency injection chains resolve access controls, session state, and payload validation without runtime exceptions.
- Static type analysis and schema validation pass with zero errors across all registered router modules.

## Enforcement

- Verified by: Continuous integration pipeline executing automated test suites and schema validation checks.
- Verified by: Peer code review verifying dependency injection patterns, router organization, and error handling contracts.
- Violation handling: Pull requests introducing direct HTTP handler implementations without router integration or missing dependency declarations will be blocked.
- Violation handling: Non-conforming endpoints must be refactored to use standardized router and dependency injection conventions.
- Exception process: Submit an architectural review request outlining technical constraints that prevent the use of standard router patterns, requiring sign-off from API module maintainers.
