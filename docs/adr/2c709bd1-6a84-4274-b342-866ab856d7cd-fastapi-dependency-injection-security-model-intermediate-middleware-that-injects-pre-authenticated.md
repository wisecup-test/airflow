---
version: 2
name: 2c709bd1-6a84-4274-b342-866ab856d7cd-fastapi-dependency-injection-security-model-intermediate-middleware-that-injects-pre-authenticated
description: "FastAPI Dependency Injection Security Model: Intermediate Middleware That Injects Pre Authenticated"
metadata:
  actual:
    adr_id: 2c709bd1-6a84-4274-b342-866ab856d7cd
    status: accepted
    candidate_group_id: 1ced8c5d-138b-4774-ab1b-24245847d3ed
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/**
---

# FastAPI Dependency Injection Security Model: Intermediate Middleware That Injects Pre Authenticated

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/api_fastapi/**`

## Context

- API endpoints across administrative, execution, and user-facing boundaries require distinct authentication mechanisms including OAuth2 tokens, Bearer tokens, and session cookies.
- Route operations enforce distinct authorization requirements ranging from broad execution scopes to fine-grained subject identity checks against path parameters.
- Repetitive authentication and authorization logic inside endpoint functions leads to inconsistent error status codes and redundant token validation across request lifecycles.

## Problem Statement

Exposing diverse API surfaces requiring distinct authentication mechanisms and authorization scopes risks duplicated security logic, inconsistent permission checks, and performance overhead from repeated cryptographic verification if validation is implemented ad-hoc inside route handlers.

## Decision

- **R-A-001** SHOULD: Intermediate middleware that injects pre-authenticated user instances into the request state SHOULD include a trusted sentinel marker that downstream security dependencies verify before trusting cached state attributes.

## Policy Block

In scope:

- All API route handlers and router declarations serving core, administrative, and execution workloads.
- Security dependency callables responsible for credential extraction, cryptographic signature verification, and scope enforcement.

Out of scope:

- Public unauthenticated endpoints that explicitly declare open access without identity requirements.
- Internal background tasks operating outside the HTTP request and response cycle.

Exceptions:

- **EXC-33-001** A trusted middleware pre-authenticates requests and sets a verifiable sentinel marker on the request state.
  Approval: Security architecture team review
  Documentation: Document the middleware integration and sentinel validation mechanics in the security architecture registry.

## Rationale

- Extracting security logic into dependency injection callables centralizes credential extraction, cryptographic parsing, and authorization enforcement into isolated, reusable units.
- Leveraging framework dependency injection guarantees that security checks execute prior to handler invocation and produce consistent HTTP 401 Unauthorized or HTTP 403 Forbidden responses.
- Request-level caching within the ASGI scope avoids redundant cryptographic signature checks across nested dependencies and downstream replay operations.

## Consequences

Positive:

- Centralized authorization rules eliminate duplicate authentication logic and reduce implementation defects across API routers.
- Composable dependency chains enable clean separation between token cryptographic validation and domain-specific scope enforcement.
- Consistent error responses and status codes are guaranteed across all protected route endpoints.

Negative:

- Tight architectural coupling to framework-specific dependency injection paradigms and request scope lifecycles.
- Debugging complex dependency chains requires understanding framework resolution order and accumulated security scopes.

## Alternatives

- Imperative authorization checks within route handler bodies (rejected)
  Rejected because: Scatters security logic across endpoints, increases code duplication, and risks omitting authorization checks during feature development.
  When valid: Simple single-file prototype scripts with minimal endpoints.
- Pure ASGI middleware authorization filtering (rejected)
  Rejected because: Middleware lacks granular access to route path parameters and accumulated endpoint security scopes needed for fine-grained authorization.
  When valid: Coarse-grained global network filtering or uniform perimeter security policies.

## Risks

- Untrusted request state modification could bypass cryptographic token validation if sentinel checks are omitted.
  Mitigation: Require security dependencies to verify explicit trusted sentinel markers alongside state attributes before honoring cached user objects.
  Owner: engineering team
- Performance degradation caused by repeated token signature verifications across nested route dependencies.
  Mitigation: Store resolved token claims in the ASGI request scope upon first verification to enable subsequent dependency calls to return cached instances.
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
- Separate token cryptographic verification from fine-grained authorization scope checks so that validation results can be reused across different authorization gates.
- Ensure security dependencies raise appropriate standard HTTP exception statuses with descriptive error details.

## Continuation Context

Verify commands:

- Discover the project testing runner and execute the authorization test suite covering security dependency resolution and token validation.
- Discover the static analysis tool configured in the repository and run type and lint checks against security modules.

Accept when:

- All security dependencies correctly validate tokens and return resolved identities across supported authentication schemes.
- Requests with invalid tokens or mismatched security scopes are rejected with standard HTTP error codes.
- Static type checking and integration test suites pass with zero errors.

## Enforcement

- Verified by: Automated continuous integration test suites verifying authorization and authentication boundary scenarios.
- Verified by: Peer review by security and core architecture team members on API route additions.
- Violation handling: Pull requests containing route handlers that bypass dependency-based security or parse raw credentials will be blocked by continuous integration checks.
- Violation handling: Unprotected endpoints identified during security review must be updated to use standard security dependencies before release.
- Exception process: Submit an architecture review request detailing the functional necessity of bypassing standard security dependencies along with mitigating perimeter controls.
- Exception process: Approval required from the security architecture team before merging exceptions.
