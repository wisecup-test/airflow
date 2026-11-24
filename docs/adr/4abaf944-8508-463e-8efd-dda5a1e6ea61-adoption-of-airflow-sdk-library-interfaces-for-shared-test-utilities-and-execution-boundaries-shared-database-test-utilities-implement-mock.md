---
version: 2
name: 4abaf944-8508-463e-8efd-dda5a1e6ea61-adoption-of-airflow-sdk-library-interfaces-for-shared-test-utilities-and-execution-boundaries-shared-database-test-utilities-implement-mock
description: "Adoption of airflow.sdk Library Interfaces for Shared Test Utilities and Execution Boundaries: Shared Database Test Utilities Implement Mock"
metadata:
  actual:
    adr_id: 4abaf944-8508-463e-8efd-dda5a1e6ea61
    status: accepted
    candidate_group_id: f73fbc6f-ed45-494e-9719-5df629d44047
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# Adoption of airflow.sdk Library Interfaces for Shared Test Utilities and Execution Boundaries: Shared Database Test Utilities Implement Mock

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/tests_common/test_utils/**`

## Context

- The codebase introduces airflow.sdk as a modernized execution boundary alongside legacy airflow core components.
- Shared testing utilities must support tests targeting both legacy runtime models and airflow.sdk execution pathways without breaking existing integration contracts.
- Plugin discovery and task execution decorators differ between legacy modules and airflow.sdk, requiring dual patching and compatibility wrapping.
- Version compatibility helpers must dynamically inspect resolved library versions to apply appropriate decorator and attribute behaviors.

## Problem Statement

As execution mechanisms migrate to airflow.sdk, test utilities and integration harnesses must support both legacy airflow execution models and new airflow.sdk interfaces. Without a standardized adoption pattern for dual-surface mocking, version compatibility branching, and hook abstractions, tests risk incomplete isolation, broken plugin discovery, and divergent test behavior between runtime modes.

## Decision

- **R-LM-001** SHOULD: Shared database test utilities SHOULD implement mock hook contracts inheriting from target hook interfaces that expose get_connection and get_conn abstractions compatible with airflow.sdk connection models.

## Policy Block

In scope:

- Shared test utilities, test harness modules, and mock fixtures that interface with airflow task execution or plugin management.
- Modules responsible for version compatibility branching between legacy airflow and airflow.sdk.

Out of scope:

- End-user DAG authoring code operating entirely within a single runtime environment.
- Standard application code outside testing and harness utilities.

## Rationale

- Adopting airflow.sdk interfaces within shared test utilities enables seamless testing of task execution and plugin discovery across runtime migrations.
- Dual patching of plugin retrieval functions ensures test fixtures apply uniformly regardless of whether components query legacy or SDK managers.
- Encapsulating version compatibility logic in dedicated utility helpers prevents duplication of conditional import logic across test files.

## Consequences

Positive:

- Enables consistent test execution across both legacy airflow and airflow.sdk execution pathways.
- Isolates test fixtures from runtime divergence through centralized dual-target mocking.
- Provides clean abstraction layers for database hooks and task failure monitoring in test suites.

Negative:

- Increases test utility complexity by requiring dual-surface maintenance whenever internal interfaces evolve.
- Introduces mock maintenance overhead when SDK and core plugin manager internal functions diverge.

## Alternatives

- Separate test suites into distinct isolation packages for core runtime versus airflow.sdk runtime (rejected)
  Rejected because: Duplicating test suites doubles maintenance overhead and fails to test cross-boundary compatibility within shared test utilities.
  When valid: When legacy and SDK runtimes are completely partitioned into separate repositories without shared utility code.
- Exclusively mock legacy airflow module paths without patching airflow.sdk equivalents (rejected)
  Rejected because: Tasks and plugins executing under airflow.sdk bypass legacy discovery mechanisms and fail to receive mocked test fixtures.
  When valid: When running in environments where airflow.sdk is not installed or enabled.
- Adopt unified dual-surface patching and compatibility wrapping within shared test utilities (accepted)
  Rejected because: None
  When valid: When supporting concurrent execution environments across core airflow and airflow.sdk.

## Risks

- Divergence between core airflow and airflow.sdk internal APIs causing mock patches to become desynchronized.
  Mitigation: Enforce contract verification in continuous integration across both runtime interfaces and validate attribute parity.
  Owner: engineering team
- Performance overhead from dual-stack context manager patching in high-volume unit tests.
  Mitigation: Scope plugin manager mocking strictly to test functions requiring plugin isolation rather than global test suite fixtures.
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
- When constructing context managers for test plugins, enter patches on both legacy and SDK plugin retrieval targets within a single exit stack.
- Maintain hook mocking harnesses by extending base hook definitions and stubbing connection retrieval methods to return mock connection objects.

## Continuation Context

Verify commands:

- Discover the project test runner configuration from the repository manifest and execute the test suite covering shared test utilities.
- Run the repository static analysis and linting checks on shared testing modules to verify import paths and contract compliance.

Accept when:

- All automated test suites utilizing shared test utilities execute and pass against both legacy and airflow.sdk runtime boundaries.
- Plugin manager mock contexts properly intercept calls to both core and airflow.sdk plugin retrieval endpoints without unhandled keyword argument errors.
- Version compatibility utilities correctly parse and branch based on resolved dependency versions without raising runtime import errors.

## Enforcement

- Verified by: Automated test suites executing shared utility tests across matrix configurations.
- Verified by: Pull request code reviews verifying adherence to dual-target patching and compatibility patterns.
- Violation handling: Test harness changes that omit airflow.sdk mock parity will be blocked at code review and fail automated test matrix checks.
- Exception process: Exceptions require documented architectural approval specifying why single-surface mocking is sufficient for a specific fixture.
