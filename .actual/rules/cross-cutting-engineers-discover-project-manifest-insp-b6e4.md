---
version: 2
name: cross-cutting-engineers-discover-project-manifest-insp-b6e4
description: "Internal Shared Utility Module (src/utils) Adoption: Engineers Discover Project Manifest Inspect Authoritative"
metadata:
  actual:
    adr_id: b6e43964-c965-46a6-8f9d-e13d67bbfeed
    status: accepted
    candidate_group_id: 22af00d2-2255-44e9-bdfe-be1f7a4dc708
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/ui/src/**
---

# Internal Shared Utility Module (src/utils) Adoption: Engineers Discover Project Manifest Inspect Authoritative

Scope: applies to `airflow-core/src/airflow/ui/src/**`.

These rules are ALWAYS ACTIVE for:
- All frontend components, page views, and asynchronous query hooks requiring common data manipulation, formatting, and cross-cutting helpers.

### Rules

- **R-LM-001** MUST: Engineers MUST discover the project manifest and inspect the authoritative lock artifact to verify dependency resolution and external boundaries before introducing any third-party dependencies into the shared utility layer.

### Verify

- Discover and run the project's static analysis and linting scripts to verify import path compliance and detect circular dependencies.
- Discover and execute the unit testing suite to confirm that all shared utility functions pass their test specifications.
- Discover and invoke the type-checking verification script to validate type signatures across utility exports and consumers.

**Accept when:**

- Static analysis and lint verification pass with zero unresolved module import errors.
- All unit tests covering shared utility functions pass successfully without regressions.
- Type checking passes across all consumers importing from the internal utility module.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks running linting, type validation, and unit test suites.
- Verified by: Peer code review verifying that reusable helpers are placed in the shared utility module rather than duplicated locally.
- Violation handling: Pull requests containing duplicated utility logic or violating unidirectional dependency boundaries are blocked until refactored.
- Exception process: Exceptions for one-off component-specific helpers must be documented with architectural rationale and approved by frontend code owners.
</enforcement>
