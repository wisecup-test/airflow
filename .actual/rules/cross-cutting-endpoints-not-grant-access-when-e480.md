---
version: 2
name: cross-cutting-endpoints-not-grant-access-when-e480
description: "JWT Token Authentication and Claims Validation with JWTValidator: Endpoints Not Grant Access When Authorization"
metadata:
  actual:
    adr_id: e480bcdc-f797-4c7f-a5c6-bf8a7c357be4
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

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- HTTP endpoints and static asset serving handlers that expose protected resources or sensitive operational assets.
- Internal token creation, exchange, and cryptographic validation components.

### Rules

- **R-A-001** MUST_NOT: Endpoints MUST NOT grant access when the Authorization header is absent, malformed, or when claim verification raises InvalidSignatureError, ExpiredSignatureError, InvalidAudienceError, ImmatureSignatureError, or InvalidIssuedAtError.

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
