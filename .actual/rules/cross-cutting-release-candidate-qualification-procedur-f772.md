---
version: 2
name: cross-cutting-release-candidate-qualification-procedur-f772
description: "airflow_breeze Test Execution and Verification Framework Adoption: Release Candidate Qualification Procedures Execute Automated"
metadata:
  actual:
    adr_id: f7720dc8-1dac-40eb-a5d3-713d83179b3a
    status: accepted
    candidate_group_id: 8f3790de-31a9-430f-af29-3ed5449978f8
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# airflow_breeze Test Execution and Verification Framework Adoption: Release Candidate Qualification Procedures Execute Automated

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- All test execution orchestration, test path resolution, containerized integration test dispatch, and release qualification workflows within developer tooling.

### Rules

- **R-TS-001** SHOULD: Release candidate qualification procedures SHOULD execute automated branch, tag, and tracking validation protocols prior to triggering test verification suites.

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
