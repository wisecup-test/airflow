---
version: 2
name: 3af40bf8-07df-4039-b073-a916bb850000-adoption-of-airflow-models-taskinstance-for-task-execution-and-state-verification-in-test-harnesses-consumers-inspect-repository-2910aaeaa9e33e34
description: "Adoption of airflow.models.taskinstance for Task Execution and State Verification in Test Harnesses: Consumers Inspect Repository Dependency Manifest Authoritative"
metadata:
  actual:
    adr_id: 3af40bf8-07df-4039-b073-a916bb850000
    status: accepted
    candidate_group_id: 85bd11a9-5371-4f0d-94fd-12a69533d81f
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# Adoption of airflow.models.taskinstance for Task Execution and State Verification in Test Harnesses: Consumers Inspect Repository Dependency Manifest Authoritative

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/tests_common/test_utils/**`

## Context

- Test harnesses require realistic task execution lifecycles, template context evaluation, and database state transitions that mirror production execution behaviors.
- Direct manipulation of low-level execution machinery in individual test cases introduces test brittleness and state pollution across test runs.
- Centralized test utility wrappers provide standardized interfaces for creating, running, and verifying task instances across integration and API test suites.

## Problem Statement

Testing workflow orchestration components requires simulating task instance execution lifecycles, rendering templates, and validating execution logs against the database. Constructing ad-hoc mocks fails to accurately reproduce model state transitions, while direct unencapsulated model mutations cause cross-test contamination and inconsistent test assertions across suites.

## Decision

- **R-LM-001** MUST: Consumers MUST inspect the repository dependency manifest and authoritative lock artifact to verify the exact resolved version of the library prior to invoking task instance execution interfaces.

## Policy Block

In scope:

- Shared test utilities and test harness helpers responsible for simulating task instance lifecycles, template rendering, and database state verification.
- Integration and functional test suites validating task execution flows and audit logging.

Out of scope:

- Unit tests evaluating pure operator logic where task execution context and database session lifecycles are unnecessary.
- Production runtime task execution managed directly by scheduler and worker execution daemons.

## Rationale

- Grounding test harnesses in airflow.models.taskinstance ensures that task state transitions, template rendering contexts, and metadata tracking adhere strictly to model definitions.
- Encapsulating task instance creation and execution contracts in shared test helpers reduces duplicate test scaffolding and standardizes test execution patterns.
- Isolating task and task instance attributes through shallow copying prevents state pollution across consecutive test executions.

## Consequences

Positive:

- Consistent test execution behavior matching runtime model semantics.
- Centralized maintenance of task instance creation, execution, and template rendering contracts across test suites.
- Reliable verification of database execution logs through standardized session querying patterns.

Negative:

- Increased test harness coupling to internal model attributes and database schema structures.
- Higher computational overhead for tests executing full task instance lifecycle flows compared to lightweight mocks.

## Alternatives

- Ad-hoc mock objects for task execution simulation (rejected)
  Rejected because: Mocking model internals fails to capture real template rendering context, state machine transitions, and database audit logging, leading to false-positive test results.
  When valid: Valid only for isolated unit tests of pure utility functions with zero model interactions.
- Direct unencapsulated manipulation of task instance models within individual test files (rejected)
  Rejected because: Sprawls repetitive boilerplate across test suites and increases the risk of cross-test state pollution due to unisolated model mutations.
  When valid: Valid only in single-file exploratory prototype scripts.

## Risks

- Changes to model attribute structures or session management in upstream releases could break shared test helpers.
  Mitigation: Verify resolved dependency versions against authoritative lock artifacts and encapsulate model interactions behind stable test utility interfaces.
  Owner: Test Infrastructure Team
- Accumulated test database state from task instance execution queries could slow down test suite runs.
  Mitigation: Ensure database transactions and query assertions are scoped strictly per test case and cleaned up post-execution.
  Owner: Test Infrastructure Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Encapsulate task instance creation and execution behind dedicated test helper functions to ensure consistent initialization and teardown across test suites.
- When asserting audit log generation, execute database select queries ordered by timestamp descending with explicit filtering on task identifiers and execution events.

## Continuation Context

Verify commands:

- Discover and run the project test execution runner targeting shared test utility suites and task instance verification tests.
- Discover and run the repository static analysis and linting checks against test utility modules to ensure adherence to model usage conventions.

Accept when:

- All task instance execution helper tests pass with clean session teardown and no cross-test state leakage.
- Static analysis validates that task instance access patterns comply with defined test utility interfaces.

## Enforcement

- Verified by: Continuous integration test execution and automated static analysis checks.
- Verified by: Mandatory peer code review for changes affecting shared test utilities or model execution harnesses.
- Violation handling: Pull requests introducing unencapsulated task instance mutations or ad-hoc task mocks in integration suites will be rejected during automated checks and code review.
- Exception process: Exceptions require documented architectural justification and explicit sign-off from the test infrastructure maintainers.
