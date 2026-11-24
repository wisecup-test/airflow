---
version: 2
name: cross-cutting-route-handlers-middleware-not-bypass-a94f
description: "Adoption of airflow.api_fastapi.auth.managers.base_auth_manager Interface Architecture: Route Handlers Middleware Not Bypass Airflow"
metadata:
  actual:
    adr_id: a94fa3eb-da88-47f2-a33f-d8d7acbb407c
    status: accepted
    candidate_group_id: 3dcba20d-500a-4e12-9280-2f63ef025ad3
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/auth/**
---

# Adoption of airflow.api_fastapi.auth.managers.base_auth_manager Interface Architecture: Route Handlers Middleware Not Bypass Airflow

Scope: applies to `airflow-core/src/airflow/api_fastapi/auth/**`.

These rules are ALWAYS ACTIVE for:
- API route handlers exposing login and token generation endpoints.
- HTTP middleware handling incoming session authentication, user resolution, and token refresh.
- Authorization models querying permissions for system resources.

### Rules

- **R-LM-001** MUST_NOT: API route handlers and middleware MUST_NOT bypass airflow.api_fastapi.auth.managers.base_auth_manager by accessing raw credential databases or unverified session headers directly.

### Verify

- Locate the repository test runner script and execute integration tests targeting API authentication and middleware components.
- Execute the repository static type verification suite against authentication manager models and route definitions.
- Discover and run the project code style and architectural linter to confirm adherence to import boundaries.

**Accept when:**

- All unit and integration test suites covering login routes, token refresh middleware, and authorization models pass without error.
- Static type verification confirms zero type errors across all authorization request dictionaries and auth manager interfaces.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline running test suites and static type verification.
- Verified by: Peer code review mandatory for changes to authentication routes, middleware, or auth manager interfaces.
- Violation handling: Pull requests bypassing the auth manager abstraction or implementing direct credential handling will be blocked from merging.
- Violation handling: Static analysis or typing violations will fail continuous integration builds.
- Exception process: Exceptions require documented architectural justification submitted to the security engineering team and formal sign-off prior to merge.
</enforcement>
