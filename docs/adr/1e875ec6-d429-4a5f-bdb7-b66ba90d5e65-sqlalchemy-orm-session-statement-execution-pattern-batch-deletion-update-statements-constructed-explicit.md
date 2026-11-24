---
version: 2
name: 1e875ec6-d429-4a5f-bdb7-b66ba90d5e65-sqlalchemy-orm-session-statement-execution-pattern-batch-deletion-update-statements-constructed-explicit
description: "SQLAlchemy ORM Session Statement Execution Pattern: Batch Deletion Update Statements Constructed Explicit"
metadata:
  actual:
    adr_id: 1e875ec6-d429-4a5f-bdb7-b66ba90d5e65
    status: accepted
    candidate_group_id: 8638214a-0b46-499d-85d0-dd50dc7c2568
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/**
---

# SQLAlchemy ORM Session Statement Execution Pattern: Batch Deletion Update Statements Constructed Explicit

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/**`
- `src/tests_common/**`

## Context

- Relational persistence interactions across database utility modules and persistence verification harnesses require consistent query construction and execution semantics.
- The adoption of SQLAlchemy ORM provides unit-of-work tracking and expression-based statement compilation for entity manipulation.
- Standardizing database access around statement execution through the session interface prevents fragmented persistence mechanisms and unmanaged transaction states.

## Problem Statement

Database access operations across data access layers and persistence test utilities risk inconsistency and resource leaks if components mix legacy query execution styles, ad-hoc raw SQL strings, and unmanaged transaction boundaries. A unified data access pattern is required to standardize how relational queries, bulk deletions, and entity mutations are prepared, executed, and committed through the session lifecycle.

## Decision

- **R-DAP-001** MUST: Batch deletion and update statements MUST be constructed using explicit delete or update expression constructs and executed through session execute.

## Policy Block

In scope:

- All data access utilities, persistence test harnesses, and model query components interacting with relational database entities.
- Components managing database lifecycle, entity assertions, or test state cleanup.

Out of scope:

- In-memory transient data structures, mock contexts, and non-relational storage systems.
- Pure analytical or static documentation generation workflows that do not communicate with relational storage.

## Rationale

- Executing statement constructs via session execute and session scalars standardizes data retrieval and DML across all database access points, eliminating divergent query construction techniques.
- Centralizing transactional state transitions in unit-of-work session methods ensures reliable consistency, deterministic resource cleanup, and predictable rollback behavior during execution failures.
- Adopting expression constructs for deletion and selection isolates schema definitions from database dialect specifics, simplifying future schema refactoring.

## Consequences

Positive:

- Consistent statement execution semantics across both test harnesses and operational persistence utilities.
- Clear transactional boundaries with explicit commit and rollback points preventing uncommitted database transactions.
- Decouples relational query construction from session-level execution mechanics.

Negative:

- Requires strict session lifecycle management to prevent connection leaks across concurrent routines.
- More verbose statement composition compared to implicit active-record convenience patterns.

## Alternatives

- Legacy ORM Query Interface (rejected)
  Rejected because: Relies on legacy session query builders that conflate statement definition with execution state and deviate from statement-based execution standards.
  When valid: Valid only when maintaining legacy extensions that do not support expression statement execution.
- Raw SQL String Execution (rejected)
  Rejected because: Lacks compile-time schema validation, introduces SQL injection vulnerabilities, and tightly couples access code to specific database dialects.
  When valid: Valid only for database dialect-specific maintenance procedures or specialized administrative migrations.

## Risks

- Unclosed sessions or missing commit calls leading to connection pool exhaustion and transaction deadlocks.
  Mitigation: Encapsulate session lifecycle in context managers that guarantee session cleanup, commit, or rollback upon block exit.
  Owner: Engineering Team
- Accidental execution of unbounded bulk delete or select statements causing resource starvation.
  Mitigation: Enforce query filtering clauses and limit criteria on statement execution across all access routines.
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
- Manage session lifecycles using structured context managers to ensure transactions are safely closed or rolled back.
- Construct query statements using expression builders before passing them to session execution methods.

## Continuation Context

Verify commands:

- discover-test-runner --suite data-access --run
- discover-static-analyzer --target persistence-modules --check

Accept when:

- All data access operations and database test suites execute cleanly with zero session leak warnings.
- Static analysis validates that all database interactions route through session statement execution and unit-of-work persistence methods.

## Enforcement

- Verified by: Automated test suite execution in continuous integration pipelines.
- Verified by: Peer code review verifying adherence to session execution standards and absence of legacy query mechanisms.
- Violation handling: Pull requests containing unapproved data access patterns or direct raw query executions will be blocked from merging until refactored.
- Exception process: Exceptions for performance-critical raw queries must be reviewed and approved by database architecture leads with documented justification.
