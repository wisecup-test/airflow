---
version: 2
name: cross-cutting-airflow-breeze-branch-defaults-centraliz-00c7
description: "Internal Module Adoption: airflow_breeze.branch_defaults for Centralized Branch Configuration: Modules Defining Environment Parameters Configuration Options"
metadata:
  actual:
    adr_id: 00c7cde4-df06-4e70-8868-20697bd9ec3b
    status: accepted
    candidate_group_id: 28cd176b-f26a-4e67-b7a4-9c27afd64a54
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Internal Module Adoption: airflow_breeze.branch_defaults for Centralized Branch Configuration: Modules Defining Environment Parameters Configuration Options

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- All command option definitions and parameter classes requiring baseline branch references or constraints references.

### Rules

- **R-LM-001** MUST: Modules defining environment parameters or configuration options MUST import default branch identifiers directly from the internal airflow_breeze.branch_defaults module rather than defining local string literals.

### Verify

- "${PROJECT_LINTER} $(find . -type f -exec grep -l 'airflow_breeze.branch_defaults' {} +)"
- "${PROJECT_TEST_RUNNER} ${PROJECT_TEST_FLAGS}"

**Accept when:**

- All parameter definitions and command options reference airflow_breeze.branch_defaults without localized literal duplication.
- The discovered verification test suite passes without assertion or configuration failures.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks in the continuous integration pipeline.
- Verified by: Peer review verification on code contributions modifying parameters or command options.
- Violation handling: Contributions containing localized hardcoded branch literals will fail continuous integration checks and require refactoring to use the shared module.
- Exception process: Exceptions require documented approval from the architecture team detailing why centralized module constants cannot be utilized.
</enforcement>
