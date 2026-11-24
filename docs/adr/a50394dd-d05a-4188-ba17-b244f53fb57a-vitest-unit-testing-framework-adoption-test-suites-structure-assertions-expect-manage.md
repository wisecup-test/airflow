---
version: 2
name: a50394dd-d05a-4188-ba17-b244f53fb57a-vitest-unit-testing-framework-adoption-test-suites-structure-assertions-expect-manage
description: "Vitest Unit Testing Framework Adoption: Test Suites Structure Assertions Expect Manage"
metadata:
  actual:
    adr_id: a50394dd-d05a-4188-ba17-b244f53fb57a
    status: accepted
    candidate_group_id: 633bdaec-608d-412c-909e-0c65956530b5
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Vitest Unit Testing Framework Adoption: Test Suites Structure Assertions Expect Manage

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Frontend user interface packages require a fast, modern unit testing framework capable of executing component and unit tests in headless environments.
- Multiple frontend packages within the application previously lacked a unified testing runner and configuration standard across user interface packages and auth manager modules.
- The codebase establishes Vitest configuration across user interface modules using vitest/config and shared test setup fixtures.

## Problem Statement

Without a standardized unit testing framework and runner configuration across user interface packages, test execution, mocking utilities, and assertion patterns diverge across distinct frontend workspaces. Inconsistent test configuration complicates continuous integration pipelines and impedes reliable verification of user interface components and layouts.

## Decision

- **R-TS-001** MUST: Test suites MUST structure assertions using expect and manage setup and teardown phases using beforeAll, beforeEach, afterEach, and afterAll imported from vitest.

## Policy Block

In scope:

- Unit and component test suites within frontend user interface packages and authentication manager UI modules.
- Test runner configurations and test lifecycle setup modules for client-side codebases.

Out of scope:

- Backend service test suites, integration test suites, and end-to-end browser automation workflows.
- Core runtime execution frameworks and workflow scheduling logic.

## Rationale

- Vitest provides first-class support for defineConfig from vitest/config, allowing test configurations to integrate seamlessly into existing frontend module configurations.
- Built-in mocking utilities through vi and assertion methods through expect provide an integrated testing toolkit without requiring disparate third-party runner dependencies.
- The presence of shared setup modules and vitest/config definitions across multiple user interface packages establishes a cohesive testing architecture across frontend workspaces.

## Consequences

Positive:

- Standardizes unit testing APIs, lifecycle hooks, and mocking patterns across frontend workspaces.
- Enables consistent test execution and configuration across distinct user interface modules.
- Eliminates configuration impedance mismatch by aligning testing definitions with build configuration via vitest/config.

Negative:

- Requires frontend developers to learn and maintain Vitest-specific mocking APIs such as vi.mock and vi.fn.
- Separates the frontend testing toolchain from the backend service testing harness, requiring distinct CI verification pipelines.

## Alternatives

- Jest Test Runner (rejected)
  Rejected because: Requires separate transform pipelines and additional configuration scaffolding rather than sharing unified configuration patterns via vitest/config.
  When valid: When working within legacy codebases where Jest is already deeply entrenched across all build tooling.
- End-to-End Browser Testing Exclusively (rejected)
  Rejected because: End-to-end browser execution introduces excessive latency and execution brittleness for isolated component logic and state hook verification.
  When valid: When verifying full multi-service workflows across deployed staging environments.

## Risks

- Divergence in Vitest versions across separate workspace packages could cause incompatible mock behavior or configuration discrepancies.
  Mitigation: Enforce workspace-wide version alignment and lockfile verification prior to executing test suites.
  Owner: Frontend Engineering Team
- Overuse of module mocking via vi.mock can lead to tests that diverge from actual runtime component behavior.
  Mitigation: Limit mocking to external network boundaries and heavyweight layout hooks, preferring testing actual component composition.
  Owner: Frontend Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Configure shared test setup modules to register DOM matchers and mock server lifecycle handlers using beforeAll, afterEach, and afterAll.
- Utilize vi.mock for modular isolation of complex layout hooks and vi.fn for capturing event callbacks in component test fixtures.

## Continuation Context

Verify commands:

- git grep -E '(from "vitest"|from "vitest/config")'
- grep -rn "defineConfig" --include="*.ts" .

Accept when:

- Frontend test configuration modules successfully configure test environments using defineConfig from vitest/config.
- All unit test suites utilizing Vitest assertion and lifecycle hooks execute cleanly without unhandled rejections.

## Enforcement

- Verified by: Continuous integration test execution stages validating all frontend test suites.
- Verified by: Automated repository linting rules prohibiting non-standard test runners and assertion libraries in frontend packages.
- Violation handling: Pull requests introducing alternative test runners or unaligned configuration patterns fail continuous integration verification and are blocked from merge.
- Exception process: Exceptions require written architectural justification and formal sign-off from frontend engineering leads before code merge.
