---
version: 2
name: cross-cutting-intermediate-middleware-that-injects-pre-2c70
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

Scope: applies to `airflow-core/src/airflow/api_fastapi/**`.

These rules are ALWAYS ACTIVE for:
- All API route handlers and router declarations serving core, administrative, and execution workloads.
- Security dependency callables responsible for credential extraction, cryptographic signature verification, and scope enforcement.

### Rules

- **R-A-001** SHOULD: Intermediate middleware that injects pre-authenticated user instances into the request state SHOULD include a trusted sentinel marker that downstream security dependencies verify before trusting cached state attributes.

### Verify

- Discover the project testing runner and execute the authorization test suite covering security dependency resolution and token validation.
- Discover the static analysis tool configured in the repository and run type and lint checks against security modules.

**Accept when:**

- All security dependencies correctly validate tokens and return resolved identities across supported authentication schemes.
- Requests with invalid tokens or mismatched security scopes are rejected with standard HTTP error codes.
- Static type checking and integration test suites pass with zero errors.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites verifying authorization and authentication boundary scenarios.
- Verified by: Peer review by security and core architecture team members on API route additions.
- Violation handling: Pull requests containing route handlers that bypass dependency-based security or parse raw credentials will be blocked by continuous integration checks.
- Violation handling: Unprotected endpoints identified during security review must be updated to use standard security dependencies before release.
- Exception process: Submit an architecture review request detailing the functional necessity of bypassing standard security dependencies along with mitigating perimeter controls.
- Exception process: Approval required from the security architecture team before merging exceptions.
</enforcement>
