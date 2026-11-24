---
version: 2
name: cross-cutting-token-validation-instances-retrieved-app-a0e3
description: "JWT Token Authentication and Claims Validation with JWTValidator: Token Validation Instances Retrieved Application State"
metadata:
  actual:
    adr_id: a0e3499b-b732-4bd4-a74b-e3158c878138
    status: accepted
    candidate_group_id: 13cbddec-a0cc-4d5b-8613-daeca936f0b9
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# JWT Token Authentication and Claims Validation with JWTValidator: Token Validation Instances Retrieved Application State

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- HTTP endpoints and static asset serving handlers that expose protected resources or sensitive operational assets.
- Internal token creation, exchange, and cryptographic validation components.

### Rules

- **R-A-001** SHOULD: Token validation instances SHOULD be retrieved from application state rather than instantiated per request to enable efficient key caching and reuse.

### Verify

- Discover and run the test suite covering token validation and endpoint authorization using the repository test runner.
- Locate the repository dependency lock artifact and verify that cryptographic token library versions match required declarations.

**Accept when:**

- All unit and integration tests covering token validation, signature verification, and path-matching assertions pass.
- Requests lacking valid authorization headers or carrying mismatched payload claims receive forbidden HTTP responses.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites validating token extraction, signature verification, and path claim authorization.
- Verified by: Continuous integration pipeline security scanning and code review procedures.
- Violation handling: Pull requests bypassing token validation or introducing unvalidated endpoint routes are blocked from merging.
- Violation handling: Runtime authentication failures generate audit warning logs and immediately return forbidden HTTP responses.
- Exception process: Submit an architectural review request to the Security Architecture Team detailing the business need and isolation controls.
- Exception process: Document approved exceptions in the repository configuration tracking system.
</enforcement>
