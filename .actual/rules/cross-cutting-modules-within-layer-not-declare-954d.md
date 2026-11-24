---
version: 2
name: cross-cutting-modules-within-layer-not-declare-954d
description: "Shared Type Definitions via airflow.api_fastapi.common.types: Modules Within Layer Not Declare Hoc"
metadata:
  actual:
    adr_id: 954db57d-8f9e-45d3-9c51-6e3bbb16f0f6
    status: accepted
    candidate_group_id: 88a71942-cff2-4e39-8fdb-28e65454e3e2
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/**
---

# Shared Type Definitions via airflow.api_fastapi.common.types: Modules Within Layer Not Declare Hoc

Scope: applies to `airflow-core/src/airflow/api_fastapi/**`.

These rules are ALWAYS ACTIVE for:
- All API route definitions, query parameters, and path operations within the API architecture.
- All request, response, and intermediate data models defined for API services.
- API schema version migration definitions.

### Rules

- **R-LM-001** MUST_NOT: Modules within the API layer MUST NOT declare ad-hoc or duplicated datetime types, relying instead on UtcDateTime from airflow.api_fastapi.common.types for all timestamp representations.

### Verify

- Discover the project static type analysis script and run it against the API packages to verify strict type conformance.
- Discover and run the project automated test suite targeting API endpoints and data model serialization.

**Accept when:**

- Static type analysis passes with zero type errors across all API routes, data models, and service modules.
- API test suites pass without schema serialization or deserialization failures across endpoints.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static type checkers and linters integrated into continuous integration pipelines.
- Verified by: Peer code review for all modifications affecting shared type modules and endpoint signatures.
- Violation handling: Pull requests with localized type duplication or non-compliant type aliases are blocked until refactored to use the common types module.
- Exception process: Exceptions require documented architectural approval and must demonstrate an unavoidable domain isolation requirement.
</enforcement>
