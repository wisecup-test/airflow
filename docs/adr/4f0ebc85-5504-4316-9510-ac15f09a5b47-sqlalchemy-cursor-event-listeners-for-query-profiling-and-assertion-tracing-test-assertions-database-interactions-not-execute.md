---
version: 2
name: 4f0ebc85-5504-4316-9510-ac15f09a5b47-sqlalchemy-cursor-event-listeners-for-query-profiling-and-assertion-tracing-test-assertions-database-interactions-not-execute
description: "SQLAlchemy Cursor Event Listeners for Query Profiling and Assertion Tracing: Test Assertions Database Interactions Not Execute"
metadata:
  actual:
    adr_id: 4f0ebc85-5504-4316-9510-ac15f09a5b47
    status: accepted
    candidate_group_id: 4c8a5146-4319-4f5c-9249-f88afcab6436
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# SQLAlchemy Cursor Event Listeners for Query Profiling and Assertion Tracing: Test Assertions Database Interactions Not Execute

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/tests_common/test_utils/**`

## Context

- Database-backed integration and unit tests require reliable mechanisms to measure query execution counts and execution timings to detect query amplification regressions.
- Direct inspection of application code cannot reliably account for ORM-generated statements or session flushing behaviors that execute at cursor execution boundaries.
- Standardized test utility modules provide query tracing and assertion capabilities across the test harness using database engine execution hooks and stack trace inspection.

## Problem Statement

ORM layers abstract database access but can obscure the volume and timing of SQL statements emitted during operations, leading to silent query amplification regressions. An automated, standardized mechanism is required to intercept, count, and trace database cursor executions to their originating call sites during test execution without altering production application logic.

## Decision

- **R-LM-001** MUST_NOT: Test assertions for database interactions MUST NOT execute monkey-patching of underlying database driver methods or mock raw connection objects when engine event listener hooks are available.

## Policy Block

In scope:

- Test assertion utilities and performance profiling harnesses verifying database query counts or execution durations.
- Integration and performance test suites monitoring ORM query efficiency.

Out of scope:

- Production runtime execution paths where query tracing listener overhead is inappropriate.
- Unit tests utilizing fully mocked data access boundaries that do not instantiate database engine connections.

## Rationale

- Subscribing to before_cursor_execute and after_cursor_execute event listeners captures all SQL statements at the driver boundary regardless of whether they originate from high-level ORM sessions or raw SQL constructs.
- Capturing stack summaries during cursor execution allows assertion failures to pinpoint the exact line of code that triggered each query, accelerating developer debugging.
- Encapsulating listener management within scoped test helpers prevents test pollution and ensures query metrics reflect only the operations within the target block.

## Consequences

Positive:

- Provides precise, automated detection of query regressions and unintended query amplification directly in test suites.
- Attributes executed queries directly to application or test call sites by pruning ORM and framework stack frames.
- Isolates query tracking metadata to connection-specific context dictionaries, avoiding global state contamination.

Negative:

- Stack frame inspection during cursor execution introduces minor performance overhead during test runs.
- Stack filtering logic must be maintained when internal ORM or session module names change across dependency updates.

## Alternatives

- Low-Level Database Driver Monkey-Patching (rejected)
  Rejected because: Directly modifying driver cursor methods tightly couples test infrastructure to specific database drivers and breaks portability across supported database backends.
  When valid: When using an execution environment that does not provide connection event hooks.
- Server-Side Query Log Capture and Parsing (rejected)
  Rejected because: Capturing queries from database server logs introduces asynchronous parsing latency, requires external service access, and cannot capture in-process client call-stack provenance.
  When valid: When executing black-box testing against external third-party managed database instances without in-process engine access.

## Risks

- Event listeners remaining attached across tests leading to inaccurate query counts or memory accumulation.
  Mitigation: Scope event listener attachment strictly within context managers or dedicated test fixtures to prevent listener leakage across unrelated tests.
  Owner: engineering team
- Internal library refactoring altering frame names and causing stack trace filters to misidentify the originating call site.
  Mitigation: Maintain a well-defined exclusion list of framework internal module names and validate stack summaries using structured frame attributes.
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
- Query tracing context managers should register listeners upon entry and unregister them upon exit to guarantee clean connection state.
- Stack frame filtering should inspect stack summary elements in reverse order to identify the immediate caller module outside the excluded framework modules.

## Continuation Context

Verify commands:

- Discover the project test runner configuration from the repository manifest and execute the test suite covering database query tracing utilities.
- Discover and run the project code quality and static analysis checks to verify event listener interfaces conform to engine contracts.

Accept when:

- All database query tracing and query counting test suites pass successfully against the configured database engine.
- Query count assertions accurately detect and report cursor execution totals and stack trace locations without leaking listener state across tests.

## Enforcement

- Verified by: Automated continuous integration test suites executing query assertion utilities.
- Verified by: Peer code review on modifications to shared test utilities and database interaction helpers.
- Violation handling: Pull requests introducing unmonitored query amplification or bypassing standard query tracing hooks are rejected during code review or test failure.
- Violation handling: Test failures reporting query count mismatches block integration pipelines until underlying database access patterns are resolved.
- Exception process: Exceptions must be requested via architectural review with evidence demonstrating why standard cursor event listeners cannot intercept the targeted database operations.
- Exception process: Temporary exemptions require tracking issues to establish alternative query regression protections.
