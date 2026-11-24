---
version: 2
name: cross-cutting-asynchronous-route-handlers-utilized-inp-6166
description: "FastAPI Framework Adoption for HTTP Services and API Routing: Asynchronous Route Handlers Utilized Input Output"
metadata:
  actual:
    adr_id: 6166b28e-a54c-4aa6-bec4-d640aacc89d5
    status: accepted
    candidate_group_id: b594e51f-ba94-4856-b35d-73a39b709566
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/**
---

# FastAPI Framework Adoption for HTTP Services and API Routing: Asynchronous Route Handlers Utilized Input Output

Scope: applies to `airflow-core/src/airflow/api_fastapi/**`.

These rules are ALWAYS ACTIVE for:
- HTTP route definitions within public and execution API boundaries
- Authentication endpoints and session management middleware
- Health and diagnostic check endpoints

### Rules

- **R-LM-001** SHOULD: Asynchronous route handlers SHOULD be utilized for input-output bound operations, ping checks, and external service communication to maintain non-blocking request pipelines.

### Verify

- Discover the project test runner and execute the automated API route and middleware test suite.
- Discover the project linter and type checker to verify route parameter annotations and dependency signatures.
- Execute the repository contract verification script to ensure generated OpenAPI schemas match declared models.

**Accept when:**

- All API endpoints return appropriate status codes and conform to declared response schemas across automated test executions.
- Dependency injection chains resolve access controls, session state, and payload validation without runtime exceptions.
- Static type analysis and schema validation pass with zero errors across all registered router modules.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration pipeline executing automated test suites and schema validation checks.
- Verified by: Peer code review verifying dependency injection patterns, router organization, and error handling contracts.
- Violation handling: Pull requests introducing direct HTTP handler implementations without router integration or missing dependency declarations will be blocked.
- Violation handling: Non-conforming endpoints must be refactored to use standardized router and dependency injection conventions.
- Exception process: Submit an architectural review request outlining technical constraints that prevent the use of standard router patterns, requiring sign-off from API module maintainers.
</enforcement>
