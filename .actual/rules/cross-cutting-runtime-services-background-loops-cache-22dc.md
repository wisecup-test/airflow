---
version: 2
name: cross-cutting-runtime-services-background-loops-cache-22dc
description: "Standard Library time Module for Process Timing and Interval Throttling: Runtime Services Background Loops Cache Invalidation"
metadata:
  actual:
    adr_id: 22dcd0ac-eb91-4bbd-a93c-ef0eb0eba484
    status: accepted
    candidate_group_id: 936380ac-53c4-4504-8617-0674ce27de0e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Standard Library time Module for Process Timing and Interval Throttling: Runtime Services Background Loops Cache Invalidation

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- In-process timing, rate limiting, and sleep pacing within worker lifecycles, cache revalidation, and streaming log readers.
- Periodic maintenance thresholds, heartbeat calculations, and worker rolling restart intervals.

### Rules

- **R-LM-001** MUST: Runtime services, background loops, and cache invalidation routines MUST utilize the standard library time module for in-process timing, interval tracking, and execution delays.

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
