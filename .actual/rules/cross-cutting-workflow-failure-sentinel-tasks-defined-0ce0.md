---
version: 2
name: cross-cutting-workflow-failure-sentinel-tasks-defined-0ce0
description: "Airflow TriggerRule.ONE_FAILED Task Sentinel Integration: Workflow Failure Sentinel Tasks Defined Airflow"
metadata:
  actual:
    adr_id: 0ce0b5ca-0523-42e4-a926-6bc4b87ac331
    status: accepted
    candidate_group_id: d830dedb-b7b1-4e1d-ba9f-bfa5d6601b13
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# Airflow TriggerRule.ONE_FAILED Task Sentinel Integration: Workflow Failure Sentinel Tasks Defined Airflow

Scope: applies to `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Workflow integration test suites requiring fast-fail behavior across parallel task executions.
- Orchestration pipelines requiring explicit failure aggregation across decoupled branches.

### Rules

- **R-IP-001** MUST: Workflow failure sentinel tasks MUST be defined using the Airflow task decorator configured with TriggerRule.ONE_FAILED and retries set to zero.

### Verify

- Discover and run the project workflow test harness to verify sentinel task trigger behavior on simulated task failures.
- Discover and run the project static analysis and linting suite to ensure sentinel task decorators follow configured trigger rules.

**Accept when:**

- Workflow test executions verify that a failure in any monitored upstream task triggers the sentinel task and marks the workflow run as failed.
- All static analysis and linting checks pass without violations against task trigger decorator conventions.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration workflow test runs.
- Verified by: Peer code review of workflow dependency graphs and task decorator configurations.
- Violation handling: Pull requests omitting required trigger rule parameters or retry constraints on sentinel tasks will fail review and automated checks.
- Exception process: Exceptions must be documented in the workflow definition and approved by the integration engineering team.
</enforcement>
