---
version: 2
name: cross-cutting-consumers-discover-project-dependency-lo-ddc2
description: "SQLAlchemy Cursor Event Listeners for Query Profiling and Assertion Tracing: Consumers Discover Project Dependency Lock Artifact"
metadata:
  actual:
    adr_id: ddc2a4c9-e17d-4319-a296-7fe2ec887715
    status: accepted
    candidate_group_id: 4c8a5146-4319-4f5c-9249-f88afcab6436
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# SQLAlchemy Cursor Event Listeners for Query Profiling and Assertion Tracing: Consumers Discover Project Dependency Lock Artifact

Scope: applies to `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Test assertion utilities and performance profiling harnesses verifying database query counts or execution durations.
- Integration and performance test suites monitoring ORM query efficiency.

### Rules

- **R-LM-001** MUST: Consumers MUST discover the project dependency lock artifact and resolve the exact locked version of SQLAlchemy and related runtime dependencies before implementing or consuming cursor execution event listeners.

### Verify

- Discover the project test runner configuration from the repository manifest and execute the test suite covering database query tracing utilities.
- Discover and run the project code quality and static analysis checks to verify event listener interfaces conform to engine contracts.

**Accept when:**

- All database query tracing and query counting test suites pass successfully against the configured database engine.
- Query count assertions accurately detect and report cursor execution totals and stack trace locations without leaking listener state across tests.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites executing query assertion utilities.
- Verified by: Peer code review on modifications to shared test utilities and database interaction helpers.
- Violation handling: Pull requests introducing unmonitored query amplification or bypassing standard query tracing hooks are rejected during code review or test failure.
- Violation handling: Test failures reporting query count mismatches block integration pipelines until underlying database access patterns are resolved.
- Exception process: Exceptions must be requested via architectural review with evidence demonstrating why standard cursor event listeners cannot intercept the targeted database operations.
- Exception process: Temporary exemptions require tracking issues to establish alternative query regression protections.
</enforcement>
