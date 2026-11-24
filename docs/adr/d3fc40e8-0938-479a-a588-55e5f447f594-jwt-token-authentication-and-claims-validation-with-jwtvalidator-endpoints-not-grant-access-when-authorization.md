---
version: 2
name: d3fc40e8-0938-479a-a588-55e5f447f594-jwt-token-authentication-and-claims-validation-with-jwtvalidator-endpoints-not-grant-access-when-authorization
description: "JWT Token Authentication and Claims Validation with JWTValidator: Endpoints Not Grant Access When Authorization"
metadata:
  actual:
    adr_id: d3fc40e8-0938-479a-a588-55e5f447f594
    status: accepted
    candidate_group_id: 13cbddec-a0cc-4d5b-8613-daeca936f0b9
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# JWT Token Authentication and Claims Validation with JWTValidator: Endpoints Not Grant Access When Authorization

Date: 2025-02-17
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Distributed services require secure, stateless authentication to authorize access to protected resources and static file endpoints without introducing shared database session state.
- Static file endpoints and internal API routes process requests that carry sensitive diagnostic data, requiring request-level authorization bound to specific resource paths.
- The codebase establishes token verification where incoming requests carry authorization credentials that are cryptographically verified against configured keys and structured claims.

## Problem Statement

Unauthenticated or unconstrained access to endpoints exposes internal data and sensitive assets to unauthorized actors. Relying on centralized session databases creates distributed bottlenecks and synchronization overhead, while failing to validate resource-specific claims allows valid tokens issued for one resource to access unrelated resources.

## Decision

- **R-A-001** MUST_NOT: Endpoints MUST NOT grant access when the Authorization header is absent, malformed, or when claim verification raises InvalidSignatureError, ExpiredSignatureError, InvalidAudienceError, ImmatureSignatureError, or InvalidIssuedAtError.

## Policy Block

In scope:

- HTTP endpoints and static asset serving handlers that expose protected resources or sensitive operational assets.
- Internal token creation, exchange, and cryptographic validation components.

Out of scope:

- Publicly accessible endpoints explicitly designated as unauthenticated.
- Direct process-internal invocations that bypass the network boundary.

Exceptions:

- **EXC-32-001** Endpoints run in isolated local development environments where authentication is explicitly disabled by configuration.
  Approval: Security Architecture Team
  Documentation: An architectural waiver documented in the project configuration repository.

## Rationale

- Asynchronous claims validation using avalidated_claims on a centralized validator ensures non-blocking verification of cryptographic signatures and standard token claims.
- Validating resource-specific claims against the requested resource path prevents confused deputy attacks and token reuse across distinct operational assets.
- Retrieving the validator from application state avoids redundant cryptographic key resolution and parsing costs across high-frequency requests.

## Consequences

Positive:

- Eliminates centralized session store dependencies by maintaining stateless cryptographic token verification across distributed nodes.
- Enforces granular, resource-bound access control at the endpoint boundary before request execution.
- Provides consistent handling and logging for expired, immature, or invalid cryptographic signatures.

Negative:

- Introduces CPU overhead for cryptographic signature verification and payload decoding on each incoming request.
- Requires distributed clock synchronization across nodes to prevent premature token expiration or future timestamp errors.

## Alternatives

- Centralized database-backed session authentication (rejected)
  Rejected because: Requires shared state across distributed nodes, adding database latency and horizontal scaling complexity for high-throughput static asset requests.
  When valid: When stateful session invalidation and immediate global revocation are strictly required over horizontal scalability.
- Static pre-shared secret header authentication (rejected)
  Rejected because: Lacks cryptographic expiration, granular audience binding, and per-resource scoped authorizations.
  When valid: When securing point-to-point communication between fixed internal nodes with static pre-shared keys.

## Risks

- Clock drift between issuing and validating nodes causes valid tokens to be rejected due to expired or future timestamps.
  Mitigation: Configure acceptable timestamp leeway on token decoders and enforce network time synchronization across infrastructure nodes.
  Owner: Infrastructure and Platform Engineering
- Mismatched resource path binding allows tokens authorized for one path to access arbitrary paths.
  Mitigation: Enforce strict equality checking between normalized request paths and token payload path claims.
  Owner: Security and API Engineering

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Store the token validator instance on application state during server initialization to preserve cached public keys and avoid per-request object creation.
- Ensure error handlers map token validation failures to forbidden status responses without leaking internal cryptographic key details in response bodies.

## Continuation Context

Verify commands:

- Discover and run the test suite covering token validation and endpoint authorization using the repository test runner.
- Locate the repository dependency lock artifact and verify that cryptographic token library versions match required declarations.

Accept when:

- All unit and integration tests covering token validation, signature verification, and path-matching assertions pass.
- Requests lacking valid authorization headers or carrying mismatched payload claims receive forbidden HTTP responses.

## Enforcement

- Verified by: Automated test suites validating token extraction, signature verification, and path claim authorization.
- Verified by: Continuous integration pipeline security scanning and code review procedures.
- Violation handling: Pull requests bypassing token validation or introducing unvalidated endpoint routes are blocked from merging.
- Violation handling: Runtime authentication failures generate audit warning logs and immediately return forbidden HTTP responses.
- Exception process: Submit an architectural review request to the Security Architecture Team detailing the business need and isolation controls.
- Exception process: Document approved exceptions in the repository configuration tracking system.
