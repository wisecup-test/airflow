---
version: 2
name: cross-cutting-when-adopting-referencing-external-libra-68f5
description: "SQLAlchemy Persistence and Mapping Access in Integration Testing Boundaries: When Adopting Referencing External Libraries Persistence"
metadata:
  actual:
    adr_id: 68f53d23-1909-4244-951d-3fdcc2b9e671
    status: accepted
    candidate_group_id: 761c2211-5906-4b3e-a1e4-0bda81a13c6b
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# SQLAlchemy Persistence and Mapping Access in Integration Testing Boundaries: When Adopting Referencing External Libraries Persistence

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All integration testing fixtures, harness definitions, and test suite boundary verifications.
- All domain services and persistence models subject to integration verification.

### Rules

- **R-TS-001** MUST: When adopting or referencing external libraries for persistence or testing verification, engineers MUST inspect the project lock file to verify the exact resolved version prior to implementation.

### Verify

- Inspect the project configuration to discover the designated test runner and execute the integration test suite.
- Discover and run the project static analysis and linting checks to ensure proper test fixture separation.

**Accept when:**

- All discovered integration test suites execute successfully with verified teardown isolation.
- Static analysis and architecture boundary checks pass with zero violations regarding test fixture separation.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline checks enforcing directory and boundary isolation.
- Verified by: Peer code review during pull request evaluations.
- Violation handling: Pull requests introducing test lifecycle constructs into production code paths are blocked from merging until refactored.
- Violation handling: Violations identified during audit must be refactored into dedicated test fixtures.
- Exception process: Submit an architecture review request detailing why integration test fixtures must interact directly with production entity lifecycle methods.
- Exception process: Obtain approval from the lead architecture reviewer prior to merging exemptions.
</enforcement>
