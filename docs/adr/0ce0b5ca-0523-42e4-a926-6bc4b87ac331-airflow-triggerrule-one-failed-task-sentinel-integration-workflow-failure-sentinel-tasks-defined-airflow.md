---
version: 2
name: 0ce0b5ca-0523-42e4-a926-6bc4b87ac331-airflow-triggerrule-one-failed-task-sentinel-integration-workflow-failure-sentinel-tasks-defined-airflow
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

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/tests_common/test_utils/**`

## Context

- Workflows orchestrating dependent tasks require deterministic failure states when any upstream step fails.
- Standard workflow execution may continue or hang waiting for unfinished branches unless an explicit failure listener interrupts processing.
- A dedicated sentinel task using upstream failure triggers can immediately flag workflow execution failure and halt downstream ambiguity.

## Problem Statement

Without explicit failure interception, workflow execution across multiple parallel branches may fail to propagate partial upstream failures to the overall workflow run in a timely manner. A standardized sentinel pattern is needed to catch single-task upstream failures and immediately fail the workflow run without retrying.

## Decision

- **R-IP-001** MUST: Workflow failure sentinel tasks MUST be defined using the Airflow task decorator configured with TriggerRule.ONE_FAILED and retries set to zero.

## Policy Block

In scope:

- Workflow integration test suites requiring fast-fail behavior across parallel task executions.
- Orchestration pipelines requiring explicit failure aggregation across decoupled branches.

Out of scope:

- Workflows with built-in fault tolerance where individual task failures are recoverable or intentionally ignored.

## Rationale

- Setting TriggerRule.ONE_FAILED allows immediate detection when any monitored upstream task fails, eliminating wasted execution time on dead-end steps.
- Enforcing zero retries ensures that failure notifications propagate instantaneously without redundant invocation cycles.
- Raising AirflowException conforms to the workflow orchestrator native contract for declaring a failed task instance.

## Consequences

Positive:

- Provides deterministic early-exit semantics for integration test workflows upon any upstream failure.
- Prevents wasted resource consumption on downstream execution branches once an upstream error occurs.
- Maintains consistent failure reporting through native workflow engine exceptions.

Negative:

- Introduces an additional sentinel task node in the execution dependency graph.
- May cause premature termination in workflows designed to tolerate partial branch failures.

## Alternatives

- Relying exclusively on default workflow engine task state cascading (rejected)
  Rejected because: Default state resolution can leave parallel branches running or require all upstream tasks to finish before overall run status updates.
  When valid: Valid for linear sequential pipelines where every task directly depends on the preceding task.
- Custom polling daemon or external status monitoring service (rejected)
  Rejected because: Increases operational complexity and introduces out-of-band dependencies outside the core workflow orchestration framework.
  When valid: Valid when monitoring cross-orchestrator distributed systems spanning distinct infrastructure providers.

## Risks

- Misconfiguration of sentinel trigger rules could trigger false positive failures on skipped upstream tasks.
  Mitigation: Strictly mandate TriggerRule.ONE_FAILED and ensure skipped tasks are explicitly distinguished from failed tasks.
  Owner: engineering team
- Over-reliance on sentinel tasks in production workflows could mask root cause error logging if exceptions are not properly detailed.
  Mitigation: Include descriptive failure context messages in the raised AirflowException payload.
  Owner: engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Sentinel tasks should be wired downstream of all parallel tasks whose failure must fail the overall execution run immediately.
- Ensure the workflow definition associates the watcher task with all necessary upstream targets using standard task dependency assignment operators.

## Continuation Context

Verify commands:

- Discover and run the project workflow test harness to verify sentinel task trigger behavior on simulated task failures.
- Discover and run the project static analysis and linting suite to ensure sentinel task decorators follow configured trigger rules.

Accept when:

- Workflow test executions verify that a failure in any monitored upstream task triggers the sentinel task and marks the workflow run as failed.
- All static analysis and linting checks pass without violations against task trigger decorator conventions.

## Enforcement

- Verified by: Automated continuous integration workflow test runs.
- Verified by: Peer code review of workflow dependency graphs and task decorator configurations.
- Violation handling: Pull requests omitting required trigger rule parameters or retry constraints on sentinel tasks will fail review and automated checks.
- Exception process: Exceptions must be documented in the workflow definition and approved by the integration engineering team.
