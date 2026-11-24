---
version: 2
name: cross-cutting-multiple-access-requirements-single-endp-3ed1
description: "FastAPI Route-Level Dependency Injection for Internal API Access Control: Multiple Access Requirements Single Endpoint Passed"
metadata:
  actual:
    adr_id: 3ed1b487-faa2-4aa0-a49c-0b7e1013731f
    status: accepted
    candidate_group_id: f50db5b0-5a17-40f0-ba16-91c75d8a2bbb
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# FastAPI Route-Level Dependency Injection for Internal API Access Control: Multiple Access Requirements Single Endpoint Passed

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- HTTP route definitions within internal API routers and execution API routers.
- Internal API endpoints exposing access to domain entities, task instances, dag runs, and runtime execution state.

### Rules

- **R-IA-001** SHOULD: Multiple access requirements for a single endpoint SHOULD be passed as distinct items within the router dependencies list to allow independent evaluation and reusable authorization composition.

### Verify

- Discover and run the project's static analysis and linting suite to ensure all internal API route definitions declare required authorization dependencies.
- Discover and execute the API test suite to verify that unauthenticated or unauthorized requests to protected endpoints return expected HTTP status codes.

**Accept when:**

- Static verification confirms that every registered internal API endpoint decorator contains the necessary route dependency declarations.
- Automated API route tests verify that endpoints reject requests failing access guard evaluation with standardized error responses.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated route verification tests integrated into the continuous integration pipeline.
- Verified by: Static analysis checks validating router decorator parameters across all API route modules.
- Verified by: Peer code review for all new or updated internal API endpoint definitions.
- Violation handling: Pull requests missing required authorization dependencies or standardized error schemas will be blocked by automated test failures and review rejection.
- Violation handling: Discovered un-guarded internal endpoints must be remediated immediately via high-priority bugfix pull requests.
- Exception process: Exceptions for unprotected internal endpoints must be reviewed and approved by the API architecture team and documented with explicit security justifications.
</enforcement>
