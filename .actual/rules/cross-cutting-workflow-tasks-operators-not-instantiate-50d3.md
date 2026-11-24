---
version: 2
name: cross-cutting-workflow-tasks-operators-not-instantiate-50d3
description: "Akeyless SDK Client Integration: Workflow Tasks Operators Not Instantiate Raw"
metadata:
  actual:
    adr_id: 50d3118e-77af-4df0-86a8-e87406ceb9bd
    status: accepted
    candidate_group_id: d4289d50-4e37-4e1c-9bde-4de6e4bca425
    evidence:
      source: deep
  scope:
    root: providers/akeyless
    paths:
      - src/airflow/**
---

# Akeyless SDK Client Integration: Workflow Tasks Operators Not Instantiate Raw

Scope: applies to `providers/akeyless/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Integration components and backend providers interacting with the Akeyless Vault Platform for secret and variable retrieval.
- Connection management routines mediating external vault authentication and session caching.

### Rules

- **R-PEA-001** MUST_NOT: Workflow tasks and operators MUST NOT instantiate raw HTTP sessions or invoke vault endpoints outside the established hook and backend abstraction layer.

### Verify

- Discover the repository test runner from the project manifest and execute the test suite covering vault hook and secret backend modules.
- Discover the static analysis tool configured in the repository and run type and style checks across the provider integration code.

**Accept when:**

- All unit and integration tests covering vault client authentication and secret retrieval execute successfully without errors.
- Static type analysis passes with zero type errors across the provider hook and backend modules.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration test execution on pull requests affecting provider integration components.
- Verified by: Automated static analysis and code review verification of hook and backend abstractions.
- Violation handling: Pull requests introducing direct HTTP vault calls or unencapsulated client creation will be blocked during code review.
- Violation handling: Build failures triggered by missing lock version verification or failing integration tests block merging.
- Exception process: Exceptions require approval from architectural maintainers with documented justification for custom protocol access.
</enforcement>
