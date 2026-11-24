---
version: 2
name: cross-cutting-components-use-functools-partial-wraps-e871
description: "functools Module Adoption for In-Memory Memoization and Callable Composition: Components Use Functools Partial Wraps Construct"
metadata:
  actual:
    adr_id: e8712f28-663a-4d86-9a6a-89caee798147
    status: accepted
    candidate_group_id: 45b55e3c-9e6b-4f15-8d89-10501bd1423e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# functools Module Adoption for In-Memory Memoization and Callable Composition: Components Use Functools Partial Wraps Construct

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Core workflow dependency checking, trigger rule evaluations, and task state computations.
- Serialization and deserialization definitions for operators, task groups, and directed acyclic graphs.
- Lifecycle monitoring, job heartbeats, and provider manager registry evaluations.

### Rules

- **R-LM-001** MAY: Components MAY use functools partial or wraps to construct higher-order functions and preserve callable metadata across decorator wrappers.

### Verify

- Discover the repository static analysis script from the project configuration and execute it to verify compliance of caching decorators and closure scopes.
- Discover the repository test execution command from the build manifest and run the dependency resolution and serialization test suite to confirm cache correctness.

**Accept when:**

- Static analysis checks pass with zero violations regarding unbounded cache usage on stateful classes.
- All dependency evaluation and serialization automated test suites execute and pass without cache-related memory leaks or stale state assertions.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks in the continuous integration pipeline scanning for improper cache decorator usage.
- Verified by: Peer code review verifying that cached closures are appropriately scoped to transaction lifetimes.
- Violation handling: Pull requests containing unbounded global caches or improperly scoped session-bound memoization will be blocked in code review.
- Violation handling: Static analysis violations in continuous integration will fail the build and require remediation prior to merge.
- Exception process: Exceptions must be submitted via an architecture review request detailing why standard functional decorators are insufficient and demonstrating memory safety.
</enforcement>
