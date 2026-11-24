---
version: 2
name: cross-cutting-multi-dimensional-query-grouping-composi-abc7
description: "itertools Module for Stream and Collection Pipeline Processing: Multi Dimensional Query Grouping Composite Database"
metadata:
  actual:
    adr_id: abc73450-6176-4b93-8282-22aad50f2131
    status: accepted
    candidate_group_id: 796cd48c-7a0e-4ce6-9ad5-1ffcdc0bfc03
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# itertools Module for Stream and Collection Pipeline Processing: Multi Dimensional Query Grouping Composite Database

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Core scheduling loops, task instance state transitions, and execution decision algorithms.
- Log streaming, buffer accumulation, and event stream processing components.
- Database query batch generation and timetable sequence evaluation routines.

### Rules

- **R-LM-001** MUST: Multi-dimensional query grouping and composite database key generation MUST use itertools.product to compute Cartesian products lazily across key sets.

### Verify

- # Discover the test runner from repository configuration and run the test suite for core stream and scheduling pipelines
- # Discover the linter and type checker from project configuration and verify compliance across all core modules

**Accept when:**

- All unit and integration tests covering scheduling loops, log stream accumulation, and timetable evaluation pass without errors.
- Static analysis and type checks report zero violations across modules implementing iterator pipelines.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites and static type checking.
- Verified by: Peer code review verifying iterator single-pass consumption and absence of unneeded collection materialization.
- Violation handling: Pull requests introducing intermediate collection allocations in scheduling hot paths will be blocked until refactored to use lazy iterators.
- Exception process: Submit an architectural exception request detailing profiling evidence where collection materialization outperforms iterator evaluation.
</enforcement>
