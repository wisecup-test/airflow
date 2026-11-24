---
version: 2
name: d8d42740-4adf-4a9b-a67d-4a346d57f888-internal-module-adoption-tests-common-test-utils-shared-test-harnesses-test-suites-not-define-custom-hoc
description: "Internal Module Adoption: tests_common.test_utils Shared Test Harnesses: Test Suites Not Define Custom Hoc"
metadata:
  actual:
    adr_id: d8d42740-4adf-4a9b-a67d-4a346d57f888
    status: accepted
    candidate_group_id: 6ac0f4e0-f518-443b-a470-f85df7b3e26e
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# Internal Module Adoption: tests_common.test_utils Shared Test Harnesses: Test Suites Not Define Custom Hoc

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/tests_common/test_utils/**`

## Context

- Test suites require reliable task execution contexts, hook connections, and plugin manager configurations without incurring external service dependencies.
- Unit and integration tests historically duplicated mock task instances and database connections across multiple test suites, leading to brittle coupling and interface drift.
- The shared module tests_common.test_utils isolates core orchestration contracts, providing in-memory task instances, simulated hook connections, and unified plugin manager patching.
- Maintaining test doubles within tests_common.test_utils ensures consistent testing interfaces across runtime core modules and distributed SDK boundaries.

## Problem Statement

Decentralized and bespoke test mocks for workflow execution contexts, database hooks, and plugin managers create maintenance friction, inconsistent behavior across test suites, and fragility during internal framework refactoring. A centralized internal testing module is required to standardize mock contracts, isolate external boundaries, and provide reusable test harnesses.

## Decision

- **R-LM-001** MUST_NOT: Test suites MUST_NOT define custom, ad-hoc task instance mocks or duplicate internal hook test doubles outside of tests_common.test_utils.

## Policy Block

In scope:

- All unit and integration test suites within the repository verifying task execution, hook connectivity, and plugin management.
- Internal test utility modules and testing support packages.

Out of scope:

- Production runtime execution paths and production module boundaries.
- End-to-end system tests requiring live external infrastructure and live database instances.

Exceptions:

- **EXC-20-001** A test explicitly validates live database connectivity or real operating environment plugin discovery.
  Approval: Architectural and Test Infrastructure Reviewers
  Documentation: Document rationale directly within the test docstring specifying the requirement for live infrastructure.

## Rationale

- Centralizing test doubles in tests_common.test_utils eliminates duplicated mock logic across distributed test suites, ensuring uniform testing behavior.
- Evidence demonstrates that MockedTaskInstance, MockedHook, and mock_plugin_manager provide standardized in-memory replacements for complex runtime contracts.
- Dual patching across runtime core and SDK namespaces within mock_plugin_manager safeguards compatibility during ongoing architectural modularization.

## Consequences

Positive:

- Eliminates repetitive test mock implementations across test suites.
- Isolates tests from external network and database dependencies, accelerating test execution.
- Standardizes cross-task data exchange simulation and plugin manager patching across runtime boundaries.

Negative:

- Changes to core runtime interfaces require synchronized updates within tests_common.test_utils.
- In-memory test doubles may mask subtle behavioral discrepancies present in live database engines or production plugin loaders.

## Alternatives

- Ad-hoc test doubles defined locally within individual test modules (rejected)
  Rejected because: Leads to code duplication, inconsistent mock behaviors, high maintenance overhead, and vulnerability to drift as internal APIs evolve.
  When valid: One-off exploratory prototyping or temporary debugging scripts not intended for repository merge.
- Requiring live external services and actual database instances for all test runs (rejected)
  Rejected because: Significantly slows test suite execution and introduces environmental flakiness into local and automated test pipelines.
  When valid: End-to-end integration and release qualification pipelines validating full infrastructure stacks.

## Risks

- MockedTaskInstance or MockedHook behavior diverging from production runtime implementations
  Mitigation: Maintain contract parity tests that validate test doubles against upstream abstract interfaces
  Owner: Testing and Core Architecture Team
- Unsynchronized namespace patching between core runtime and SDK plugin managers
  Mitigation: Enforce dual-patching invariants within mock_plugin_manager and validate with regression test coverage
  Owner: Framework Infrastructure Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Discover the shared test utility namespace within the repository and import MockedTaskInstance for tests requiring in-memory task state manipulation.
- Apply mock_plugin_manager context handlers to ensure both legacy and SDK plugin discovery namespaces are appropriately intercepted during plugin test execution.

## Continuation Context

Verify commands:

- "${PROJECT_TEST_RUNNER}" discover --pattern="test_utils*"
- "${PROJECT_VERIFY_SCRIPT}"

Accept when:

- The shared testing module suite executes successfully with zero failures.
- All test double interfaces adhere to upstream core runtime and SDK class contracts.

## Enforcement

- Verified by: Automated continuous integration test suites.
- Verified by: Peer code review for pull requests modifying or adding test harnesses.
- Violation handling: Pull requests introducing ad-hoc mocks where shared test doubles exist in tests_common.test_utils will be blocked.
- Violation handling: Engineers must refactor redundant test doubles to utilize the shared module.
- Exception process: Submit an architectural review request outlining why existing shared test utilities cannot accommodate the testing requirement.
