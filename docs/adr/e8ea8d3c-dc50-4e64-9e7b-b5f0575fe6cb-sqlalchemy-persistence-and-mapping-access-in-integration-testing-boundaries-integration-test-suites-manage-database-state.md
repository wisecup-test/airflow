---
version: 2
name: e8ea8d3c-dc50-4e64-9e7b-b5f0575fe6cb-sqlalchemy-persistence-and-mapping-access-in-integration-testing-boundaries-integration-test-suites-manage-database-state
description: "SQLAlchemy Persistence and Mapping Access in Integration Testing Boundaries: Integration Test Suites Manage Database State"
metadata:
  actual:
    adr_id: e8ea8d3c-dc50-4e64-9e7b-b5f0575fe6cb
    status: accepted
    candidate_group_id: 761c2211-5906-4b3e-a1e4-0bda81a13c6b
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# SQLAlchemy Persistence and Mapping Access in Integration Testing Boundaries: Integration Test Suites Manage Database State

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Static analysis flagged numerous occurrences of dictionary property lookups and relational entity deletions across core service endpoints, models, and migration scripts under integration testing categories.
- The observed code patterns represent production application logic and standard persistence operations rather than a deliberate integration testing framework or harness architecture.
- A clear architectural boundary must distinguish production data manipulation patterns from integration testing fixtures and verification harnesses.

## Problem Statement

Runtime data retrieval and database entity deletion operations within production services risk being conflated with integration test fixtures, leading to ambiguous boundaries between production state handling and integration verification logic.

## Decision

- **R-TS-001** SHOULD: Integration test suites SHOULD manage database state cleanup through transactional rollbacks or dedicated fixture teardown hooks rather than inline business logic deletion calls.

## Policy Block

In scope:

- All integration testing fixtures, harness definitions, and test suite boundary verifications.
- All domain services and persistence models subject to integration verification.

Out of scope:

- Pure unit tests operating on isolated, mocked components without external dependencies.
- Production runtime implementations independent of test execution lifecycles.

## Rationale

- The intermediate representation evidence demonstrates that basic dictionary access and database deletion calls were captured across runtime services, indicating false-positive classification rather than an adopted integration testing pattern.
- Formally segregating integration test fixtures from production persistence operations ensures deterministic test runs and prevents test logic leakage into production.

## Consequences

Positive:

- Prevents test lifecycle side effects from leaking into production execution paths.
- Clarifies boundary definitions between domain data operations and integration verification.
- Improves reliability and predictability of integration test fixture lifecycles.

Negative:

- Requires ongoing diligence to maintain clear separation between testing utilities and domain runtime logic.
- Static analysis rules must be maintained to prevent false positives between runtime operations and testing assertions.

## Alternatives

- Direct inline state mutation and manual entity deletion within production handlers for test synchronization (rejected)
  Rejected because: Couples testing lifecycle concerns with core runtime handlers and compromises service boundary isolation.
  When valid: Never valid in production architectures.
- Dedicated integration test harness with transactional rollbacks and isolated test fixtures (accepted)
  When valid: When verifying cross-boundary component interactions against persistence backends.

## Risks

- Ambiguity between production data cleanup logic and integration test fixture teardown.
  Mitigation: Enforce strict module boundaries ensuring integration test fixtures reside exclusively within designated test suites.
  Owner: Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Inspect the project repository to identify designated test directories and test runner configurations prior to introducing integration test suites.
- Ensure all database state setup and teardown operations in integration tests utilize managed transaction boundaries rather than hardcoded entity deletions.

## Continuation Context

Verify commands:

- Inspect the project configuration to discover the designated test runner and execute the integration test suite.
- Discover and run the project static analysis and linting checks to ensure proper test fixture separation.

Accept when:

- All discovered integration test suites execute successfully with verified teardown isolation.
- Static analysis and architecture boundary checks pass with zero violations regarding test fixture separation.

## Enforcement

- Verified by: Automated continuous integration pipeline checks enforcing directory and boundary isolation.
- Verified by: Peer code review during pull request evaluations.
- Violation handling: Pull requests introducing test lifecycle constructs into production code paths are blocked from merging until refactored.
- Violation handling: Violations identified during audit must be refactored into dedicated test fixtures.
- Exception process: Submit an architecture review request detailing why integration test fixtures must interact directly with production entity lifecycle methods.
- Exception process: Obtain approval from the lead architecture reviewer prior to merging exemptions.
