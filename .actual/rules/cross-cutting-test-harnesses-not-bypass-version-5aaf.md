---
version: 2
name: cross-cutting-test-harnesses-not-bypass-version-5aaf
description: "Adoption of airflow.sdk Library Interfaces for Shared Test Utilities and Execution Boundaries: Test Harnesses Not Bypass Version Compatibility"
metadata:
  actual:
    adr_id: 5aaf293f-1214-4ecc-a3c5-5827926e49d5
    status: accepted
    candidate_group_id: f73fbc6f-ed45-494e-9719-5df629d44047
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# Adoption of airflow.sdk Library Interfaces for Shared Test Utilities and Execution Boundaries: Test Harnesses Not Bypass Version Compatibility

Scope: applies to `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Shared test utilities, test harness modules, and mock fixtures that interface with airflow task execution or plugin management.
- Modules responsible for version compatibility branching between legacy airflow and airflow.sdk.

### Rules

- **R-LM-001** MUST_NOT: Test harnesses MUST_NOT bypass version-compatibility wrappers when importing task decorators or execution context objects across core airflow and airflow.sdk boundaries.

### Verify

- Discover the project test runner configuration from the repository manifest and execute the test suite covering shared test utilities.
- Run the repository static analysis and linting checks on shared testing modules to verify import paths and contract compliance.

**Accept when:**

- All automated test suites utilizing shared test utilities execute and pass against both legacy and airflow.sdk runtime boundaries.
- Plugin manager mock contexts properly intercept calls to both core and airflow.sdk plugin retrieval endpoints without unhandled keyword argument errors.
- Version compatibility utilities correctly parse and branch based on resolved dependency versions without raising runtime import errors.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites executing shared utility tests across matrix configurations.
- Verified by: Pull request code reviews verifying adherence to dual-target patching and compatibility patterns.
- Violation handling: Test harness changes that omit airflow.sdk mock parity will be blocked at code review and fail automated test matrix checks.
- Exception process: Exceptions require documented architectural approval specifying why single-surface mocking is sufficient for a specific fixture.
</enforcement>
