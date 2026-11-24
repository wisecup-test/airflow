---
version: 2
name: cross-cutting-execution-routines-involving-containeriz-cee7
description: "airflow_breeze Test Execution and Verification Framework Adoption: Execution Routines Involving Containerized Composite Environments"
metadata:
  actual:
    adr_id: cee7f13f-324f-4951-badd-922c69f00b44
    status: accepted
    candidate_group_id: 8f3790de-31a9-430f-af29-3ed5449978f8
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# airflow_breeze Test Execution and Verification Framework Adoption: Execution Routines Involving Containerized Composite Environments

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- All test execution orchestration, test path resolution, containerized integration test dispatch, and release qualification workflows within developer tooling.

### Rules

- **R-TS-001** MUST: Execution routines involving containerized or composite environments MUST invoke pre-execution verification contracts to validate image integrity prior to launching test suites.

### Verify

- Discover and run the project test execution verification suite using the repository-defined test runner command.
- Execute repository static analysis and linting checks against developer tooling test utilities to confirm contract compliance.

**Accept when:**

- The project test execution and verification suite passes across all configured test types and validation routines.
- Test path resolution and argument mapping interfaces execute without unhandled configuration exceptions.
- Pre-execution environment and image verification contracts successfully validate deployment readiness before test dispatch.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline execution verifying all test suites pass through standardized orchestration wrappers.
- Verified by: Static analysis and peer review ensuring no direct, unmediated test runner execution scripts bypass the centralized testing utilities.
- Violation handling: Build failures triggered on pull requests that bypass centralized test path resolution or pre-execution validation routines.
- Violation handling: Rejection of release qualification workflows that omit standard release candidate verification protocols.
- Exception process: Submit an architectural exception request detailing why a specific test suite or validation routine cannot utilize the centralized orchestration contracts.
- Exception process: Obtain approval from the testing and release management engineering maintainers before implementing custom invocation wrappers.
</enforcement>
