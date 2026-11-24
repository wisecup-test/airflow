---
version: 2
name: cross-cutting-route-handlers-not-instantiate-database-520e
description: "FastAPI Framework Adoption for HTTP API Routing and Dependency Injection: Route Handlers Not Instantiate Database Sessions"
metadata:
  actual:
    adr_id: 520ee480-079e-49f1-93e1-6831a19b931a
    status: accepted
    candidate_group_id: b7343a4c-5ee2-48c1-99aa-91db6460b471
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/**
---

# FastAPI Framework Adoption for HTTP API Routing and Dependency Injection: Route Handlers Not Instantiate Database Sessions

Scope: applies to `airflow-core/src/airflow/api_fastapi/**`.

These rules are ALWAYS ACTIVE for:
- HTTP API endpoint declarations across public, user-interface, and internal execution service layers.
- Route dependency declarations for authorization, authentication, query parameter parsing, and database session injection.

### Rules

- **R-LM-001** MUST_NOT: Route handlers MUST_NOT instantiate database sessions directly or execute business logic without declaring corresponding Depends dependencies on route definitions or path operation decorators.

### Verify

- Discover and run the project static analysis suite to verify that all API endpoint route definitions conform to framework dependency conventions.
- Discover and execute the test runner to validate that endpoint authorization and dependency injection bindings execute successfully across route suites.
- Discover and execute the project schema generation script to verify OpenAPI contract generation from registered route handlers.

**Accept when:**

- Static analysis checks pass with zero violations of route dependency declarations.
- All API endpoint tests pass, confirming correct resolution of dependency injection providers.
- API specification generation completes successfully without schema definition errors.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration linting and type-checking suites.
- Verified by: Peer code review verification of dependency injection usage on all route endpoints.
- Verified by: Contract verification tests comparing generated API schemas against endpoint definitions.
- Violation handling: Pull requests introducing route handlers without declarative dependency injection are blocked at automated review.
- Violation handling: Endpoints missing authorization dependencies trigger automated test failures during pipeline execution.
- Exception process: Submit an architectural review request to the API architecture team documenting why framework-provided dependency injection cannot satisfy the integration requirement.
- Exception process: Exceptions must define alternative security validation controls approved by security engineering before merging.
</enforcement>
