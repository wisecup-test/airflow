---
version: 2
name: ce7bd547-dbf3-47a5-95b2-e4cc53ca5b80-fastapi-application-boundary-middleware-structuring-before-implementing-updating-any-middleware-components
description: "FastAPI Application Boundary Middleware Structuring: Before Implementing Updating Any Middleware Components"
metadata:
  actual:
    adr_id: ce7bd547-dbf3-47a5-95b2-e4cc53ca5b80
    status: accepted
    candidate_group_id: a4db21fa-e788-4d9d-bd4b-0e168259c57e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/**
---

# FastAPI Application Boundary Middleware Structuring: Before Implementing Updating Any Middleware Components

Date: 2025-05-15
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/api_fastapi/**`

## Context

- Multiple HTTP service boundaries require consistent enforcement of cross-cutting concerns including distributed tracing, token lifecycle management, access logging, and transport compression.
- Decentralizing boundary behaviors across individual endpoint handlers leads to inconsistent header handling and duplicated maintenance overhead.
- The application framework provides an ASGI-compatible architecture supporting composable middleware stacks through add_middleware and custom BaseHTTPMiddleware dispatch interceptors.

## Problem Statement

In an evolving HTTP API architecture with multiple specialized service sub-applications, cross-cutting boundary protocols must be consistently enforced across all endpoints without polluting route handlers or introducing divergent header and authentication handling across service boundaries.

## Decision

- **R-ISP-001** MUST: Before implementing or updating any middleware components, developers MUST inspect the repository lock artifact to determine the exact resolved framework version and verify API compatibility against that version reference.

## Policy Block

In scope:

- FastAPI application factories and sub-application entry points exposing HTTP service interfaces.
- Cross-cutting boundary protocols including request correlation tracing, authentication header inspection, access logging, and transport compression.

Out of scope:

- Domain business logic, database transaction lifecycles, and endpoint-specific payload validation handled via route dependency injection.
- Internal service communication mechanisms operating outside HTTP boundaries.

## Rationale

- Centralizing protocol-level operations in dedicated middleware components ensures uniform enforcement of security headers, token refresh flows, and distributed tracing across all endpoints.
- Utilizing BaseHTTPMiddleware encapsulates asynchronous request interception and response header injection within self-contained, testable units.
- Application-level middleware registration via add_middleware provides explicit composition of boundary concerns in a controlled execution sequence.

## Consequences

Positive:

- Uniform propagation of tracing correlation identifiers and security headers across all API endpoints.
- Route handlers remain decoupled from transport-level protocol plumbing and access logging logic.
- Modular middleware components can be reused across core and execution service sub-applications.

Negative:

- Layered dispatch wrappers introduce call stack depth and minor per-request latency overhead.
- Middleware registration order is strictly sensitive and improper ordering can lead to missing headers or unhandled exceptions.

## Alternatives

- Route-level dependency injection for cross-cutting request and response concerns (rejected)
  Rejected because: Dependency injection executes inside endpoint resolution and cannot intercept or mutate transport-level response streams or global headers.
  When valid: When an operation requires access to parsed request parameters or transactional database sessions rather than raw HTTP transport streams.
- External reverse proxy gateway handling all cross-cutting boundary protocols (rejected)
  Rejected because: Externalizing application-level token reissue and internal correlation context variable binding disconnects telemetry from internal asynchronous execution contexts.
  When valid: When services are heterogeneous microservices managed by external platform infrastructure without shared application-level state.

## Risks

- Middleware execution order sensitivity where upstream middleware fails to access headers modified by downstream middleware.
  Mitigation: Document required middleware registration order and validate sequence correctness using automated boundary integration tests.
  Owner: Engineering team
- BaseHTTPMiddleware overhead causing latency degradation on high-throughput asynchronous paths.
  Mitigation: Benchmark middleware latency and keep dispatch logic lightweight without blocking asynchronous event loops.
  Owner: Engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Ensure middleware dispatch methods handle internal exceptions gracefully without interrupting the downstream HTTP response flow when secondary operations fail.
- Bind context variables before invoking downstream handlers to guarantee asynchronous task isolation across concurrent requests.

## Continuation Context

Verify commands:

- Discover and run the project static analysis and linting scripts to verify that middleware registrations conform to application factory conventions.
- Discover and run the project test suite for HTTP service entry points to confirm request and response header propagation across all active middleware layers.

Accept when:

- All application factory definitions register required boundary middleware components through add_middleware in the specified sequence.
- Cross-cutting request correlation and security header handling are verified through automated integration tests without route-level intervention.

## Enforcement

- Verified by: Static analysis and code review ensuring middleware registration in application factory routines.
- Verified by: Automated integration tests validating header propagation and response modification across service boundaries.
- Violation handling: Pull requests duplicating cross-cutting protocol logic in route handlers or bypassing standard middleware registration are rejected.
- Exception process: Architectural exceptions for endpoints requiring raw ASGI bypass or custom stream handling must be submitted for engineering review and documented in the repository.
