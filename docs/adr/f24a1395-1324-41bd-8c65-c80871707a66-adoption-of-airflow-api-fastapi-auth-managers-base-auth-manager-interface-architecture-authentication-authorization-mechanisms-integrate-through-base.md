---
version: 2
name: f24a1395-1324-41bd-8c65-c80871707a66-adoption-of-airflow-api-fastapi-auth-managers-base-auth-manager-interface-architecture-authentication-authorization-mechanisms-integrate-through-base
description: "Adoption of airflow.api_fastapi.auth.managers.base_auth_manager Interface Architecture: Authentication Authorization Mechanisms Integrate Through Base"
metadata:
  actual:
    adr_id: f24a1395-1324-41bd-8c65-c80871707a66
    status: accepted
    candidate_group_id: 3dcba20d-500a-4e12-9280-2f63ef025ad3
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/auth/**
---

# Adoption of airflow.api_fastapi.auth.managers.base_auth_manager Interface Architecture: Authentication Authorization Mechanisms Integrate Through Base

Date: 2025-05-14
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/api_fastapi/auth/**`

## Context

- The API layer requires a decoupled and extensible authentication and authorization architecture capable of supporting diverse identity management backends.
- Authentication endpoints, session refresh middleware, and fine-grained resource permission checks require a unified abstraction to avoid disparate access control implementations.
- Centralizing identity resolution, token generation, and authorization query structures within an auth manager interface ensures uniform security policy application across API endpoints.

## Problem Statement

Hardcoding authentication routines and access control evaluations directly inside API endpoint handlers leads to inconsistent token handling, duplicated authorization logic, and tight coupling to specific authentication backends. The API framework requires a unified, pluggable contract for user authentication, token refresh lifecycle management, and batch resource authorization.

## Decision

- **R-LM-001** MUST: All API authentication and authorization mechanisms MUST integrate through the base auth manager interface provided by airflow.api_fastapi.auth.managers.base_auth_manager.

## Policy Block

In scope:

- API route handlers exposing login and token generation endpoints.
- HTTP middleware handling incoming session authentication, user resolution, and token refresh.
- Authorization models querying permissions for system resources.

Out of scope:

- Public unauthenticated documentation and health check endpoints that require no user context.
- Internal communication channels utilizing independent mutual transport security mechanisms.

## Rationale

- Abstracting authentication and authorization through airflow.api_fastapi.auth.managers.base_auth_manager permits swapping or extending identity providers without modifying endpoint routes or middleware pipelines.
- Delegating token generation and refresh logic to dedicated auth manager methods ensures consistent cryptographic operations and centralized user state injection.
- Defining strongly typed authorization request models guarantees compile-time verification of resource permission checks across diverse access entities.

## Consequences

Positive:

- Decouples HTTP routing and middleware mechanics from authentication provider business logic and identity storage.
- Standardizes session renewal, token rotation, and cookie security flags across all authenticated endpoints.
- Establishes uniform, strongly typed request definitions for consistent authorization queries across disparate resource types.

Negative:

- Introduces interface indirection requiring developers to adhere strictly to auth manager protocols for new endpoint features.
- Requires careful synchronization between auth manager token generation and middleware cookie scope configuration to prevent redirect loops.

## Alternatives

- Direct endpoint authentication without auth manager abstraction (rejected)
  Rejected because: Couples individual route handlers directly to specific credential stores and prevents pluggable identity provider implementations.
  When valid: In single-tenant applications with a fixed, immutable authentication scheme and no authorization complexity.
- Edge gateway authentication passing pre-authenticated identity headers (rejected)
  Rejected because: Fails to provide programmatic resource-level authorization checks and fine-grained batch permission queries within the API application layer.
  When valid: In architectures where all access control and permission evaluations are offloaded completely to an external API gateway.

## Risks

- Mismatched cookie path or scheme configurations between middleware and client causing authentication redirect loops.
  Mitigation: Ensure middleware explicitly purges root-scoped cookies and standardizes cookie path resolution based on central configuration.
  Owner: engineering team
- Unauthorized state mutation on the request context by untrusted middleware.
  Mitigation: Enforce trust sentinels alongside request user state assignments that can be verified during downstream dependency resolution.
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
- Auth manager implementations must encapsulate credential validation within dedicated methods and return standardized token payloads.
- Middleware handling user refresh must catch token expiration exceptions and clear authentication cookies cleanly to avoid unhandled runtime faults.

## Continuation Context

Verify commands:

- Locate the repository test runner script and execute integration tests targeting API authentication and middleware components.
- Execute the repository static type verification suite against authentication manager models and route definitions.
- Discover and run the project code style and architectural linter to confirm adherence to import boundaries.

Accept when:

- All unit and integration test suites covering login routes, token refresh middleware, and authorization models pass without error.
- Static type verification confirms zero type errors across all authorization request dictionaries and auth manager interfaces.

## Enforcement

- Verified by: Automated continuous integration pipeline running test suites and static type verification.
- Verified by: Peer code review mandatory for changes to authentication routes, middleware, or auth manager interfaces.
- Violation handling: Pull requests bypassing the auth manager abstraction or implementing direct credential handling will be blocked from merging.
- Violation handling: Static analysis or typing violations will fail continuous integration builds.
- Exception process: Exceptions require documented architectural justification submitted to the security engineering team and formal sign-off prior to merge.
