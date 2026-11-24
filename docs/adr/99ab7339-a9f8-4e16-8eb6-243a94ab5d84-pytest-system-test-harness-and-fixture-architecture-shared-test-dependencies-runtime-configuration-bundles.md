---
version: 2
name: 99ab7339-a9f8-4e16-8eb6-243a94ab5d84-pytest-system-test-harness-and-fixture-architecture-shared-test-dependencies-runtime-configuration-bundles
description: "Pytest System Test Harness and Fixture Architecture: Shared Test Dependencies Runtime Configuration Bundles"
metadata:
  actual:
    adr_id: 99ab7339-a9f8-4e16-8eb6-243a94ab5d84
    status: accepted
    candidate_group_id: 3b69d00e-c8ab-4633-be37-6ffb93fb9efd
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# Pytest System Test Harness and Fixture Architecture: Shared Test Dependencies Runtime Configuration Bundles

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/tests_common/test_utils/**`

## Context

- System and integration test suites require clean runtime environments, deterministic logging inspection, and safe teardown of external connection credentials.
- Codebases managing complex external integrations require standardized test harness utilities rather than ad-hoc setup and cleanup logic distributed across individual test files.
- The shared testing utilities package adopts test framework markers and autouse fixtures to standardize environment isolation, warning collection, and database transaction lifecycles.

## Problem Statement

Without a standardized testing harness and fixture convention, end-to-end and system test suites suffer from mutable state leakage, unmanaged log directories, and undeclared external dependency requirements. Individual tests tend to implement disparate setup and teardown routines, leading to test flakiness and difficult diagnostic tracing.

## Decision

- **R-TS-001** MUST: Shared test dependencies and runtime configuration bundles MUST be injected into test classes using `pytest.mark.usefixtures` or fixture parameter injection.

## Policy Block

In scope:

- All system, performance, and external integration test suites.
- Shared test utility modules providing harness functions, fixtures, or connection providers.

Out of scope:

- Pure unit tests executing with in-memory mocks that do not touch external services, databases, or logging filesystems.
- Application production code outside the test tree.

Exceptions:

- **EXC-39-001** A test requires custom manual process spawning outside standard fixture scopes
  Approval: Testing Architecture Lead
  Documentation: Document the manual cleanup procedure in the test class docstring

## Rationale

- Evidence demonstrates consistent reliance on pytest markers and fixtures across shared utility helpers to govern system test state.
- Autouse fixtures with scoped cleanup prevent filesystem pollution and database reset collisions between test executions.
- Centralizing system connection and deprecation testing into standardized pytest fixtures eliminates duplicate helper boilerplate and prevents test flakiness.

## Consequences

Positive:

- Deterministic isolation between system test runs eliminates cascading failures caused by leftover log files or database records.
- Selective execution of system test suites prevents slow external integrations from blocking rapid unit test feedback.
- Standardized deprecation assertion patterns ensure breaking changes are detected systematically across releases.

Negative:

- Higher cognitive overhead for test authors who must understand fixture scopes, autouse behaviors, and marker semantics.
- Debugging fixture setup and teardown lifecycle errors can be more complex than inspecting linear test scripts.

## Alternatives

- Standard library unit test cases with manual setup and teardown methods (rejected)
  Rejected because: Lacks native composable fixture scoping, dynamic marker filtering, and clean yield-based teardown mechanisms
  When valid: When testing minimal standalone scripts with zero external dependencies
- Ad-hoc test utility functions called imperatively at the start and end of each test (rejected)
  Rejected because: Prone to omission when tests encounter unexpected exceptions, leading to unhandled state leakage
  When valid: Never in automated continuous integration test suites

## Risks

- Improper fixture scoping causing state leakage across concurrent test executions
  Mitigation: Enforce strict class or function scoping on fixtures that mutate database or filesystem state
  Owner: Testing and QA Engineering
- Unmarked system tests accidentally running during rapid unit test cycles, increasing execution latency
  Mitigation: Configure continuous integration runner filters to exclude tests tagged with system markers by default during rapid checks
  Owner: Infrastructure Engineering

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Organize shared fixtures in central test utility packages and import them into test modules to ensure uniform cleanup semantics across system suites.
- Structure test cleanup using yield statements inside fixture definitions so teardown logic executes regardless of test assertion outcomes.

## Continuation Context

Verify commands:

- Discover the test runner from the project configuration and execute tests matching the system marker.
- Discover and run the project static analysis and linting scripts to verify adherence to fixture scoping rules.
- Execute the test discovery command to ensure all system tests are properly registered under the test harness.

Accept when:

- All tests marked with system tags execute to completion with clean post-run state verification.
- Test execution produces zero unhandled resource warnings or uncleared filesystem artifacts.
- All deprecation warnings captured by warning fixtures match declared criteria.

## Enforcement

- Verified by: Automated continuous integration test suite pipelines executing marker-filtered test runs.
- Verified by: Code review checks validating fixture usage and marker annotations on new test cases.
- Violation handling: Pull requests containing untagged system tests or unmanaged state fixtures will fail automated verification and be blocked from merging.
- Violation handling: Tests causing environment contamination will be disabled until fixture teardown is corrected.
- Exception process: Exceptions require approval from the Testing Architecture Lead documented in the test module docstring.
