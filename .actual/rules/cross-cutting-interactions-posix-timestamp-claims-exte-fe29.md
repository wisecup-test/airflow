---
version: 2
name: cross-cutting-interactions-posix-timestamp-claims-exte-fe29
description: "Standard Library time Module for Process Timing and Interval Throttling: Interactions Posix Timestamp Claims External Security"
metadata:
  actual:
    adr_id: fe29fd2e-b289-4dc8-968e-7fc9dcd04ea1
    status: accepted
    candidate_group_id: 936380ac-53c4-4504-8617-0674ce27de0e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Standard Library time Module for Process Timing and Interval Throttling: Interactions Posix Timestamp Claims External Security

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- In-process timing, rate limiting, and sleep pacing within worker lifecycles, cache revalidation, and streaming log readers.
- Periodic maintenance thresholds, heartbeat calculations, and worker rolling restart intervals.

### Rules

- **R-LM-001** SHOULD: Interactions with POSIX timestamp claims in external security tokens SHOULD restrict time.time evaluations exclusively to epoch comparison boundaries while delegating persistent timestamps to timezone-aware UTC types.

### Verify

- Discover and execute the project test runner across unit and integration test suites to ensure timing and interval routines pass.
- Discover and execute the project static analysis and linting scripts to verify compliance with standard library import conventions.

**Accept when:**

- All automated unit and integration tests executing process scheduling, cache revalidation, and stream polling pass successfully.
- Static analysis validates that in-memory elapsed interval calculations rely on monotonic clock sources without wall-clock drift vulnerabilities.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites and static analysis linters.
- Verified by: Peer code review for all pull requests affecting runtime process loops, caching intervals, and security token lifetimes.
- Violation handling: Pull requests utilizing wall-clock time for interval measurements or persisting monotonic values are flagged and blocked during code review.
- Exception process: Submit an architectural review request detailing external protocol constraints that necessitate non-monotonic timing primitives.
</enforcement>
