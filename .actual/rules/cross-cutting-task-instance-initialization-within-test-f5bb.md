---
version: 2
name: cross-cutting-task-instance-initialization-within-test-f5bb
description: "Adoption of airflow.models.taskinstance for Task Execution and State Verification in Test Harnesses: Task Instance Initialization Within Test Helpers"
metadata:
  actual:
    adr_id: f5bbd8b9-7a7b-470f-8ae4-2f61f338d2b8
    status: accepted
    candidate_group_id: 85bd11a9-5371-4f0d-94fd-12a69533d81f
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# Adoption of airflow.models.taskinstance for Task Execution and State Verification in Test Harnesses: Task Instance Initialization Within Test Helpers

Scope: applies to `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Shared test utilities and test harness helpers responsible for simulating task instance lifecycles, template rendering, and database state verification.
- Integration and functional test suites validating task execution flows and audit logging.

### Rules

- **R-LM-001** MUST: Task instance initialization within test helpers MUST execute create_task_instance to establish valid model state before invoking run_task_instance or run.

### Verify

- Discover and run the project test execution runner targeting shared test utility suites and task instance verification tests.
- Discover and run the repository static analysis and linting checks against test utility modules to ensure adherence to model usage conventions.

**Accept when:**

- All task instance execution helper tests pass with clean session teardown and no cross-test state leakage.
- Static analysis validates that task instance access patterns comply with defined test utility interfaces.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration test execution and automated static analysis checks.
- Verified by: Mandatory peer code review for changes affecting shared test utilities or model execution harnesses.
- Violation handling: Pull requests introducing unencapsulated task instance mutations or ad-hoc task mocks in integration suites will be rejected during automated checks and code review.
- Exception process: Exceptions require documented architectural justification and explicit sign-off from the test infrastructure maintainers.
</enforcement>
