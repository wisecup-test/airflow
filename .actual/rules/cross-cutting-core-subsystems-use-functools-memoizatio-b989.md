---
version: 2
name: cross-cutting-core-subsystems-use-functools-memoizatio-b989
description: "functools Module Adoption for In-Memory Memoization and Callable Composition: Core Subsystems Use Functools Memoization Decorators"
metadata:
  actual:
    adr_id: b98943bf-dfaa-4ee4-93e6-a80cd1a31f14
    status: accepted
    candidate_group_id: 45b55e3c-9e6b-4f15-8d89-10501bd1423e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# functools Module Adoption for In-Memory Memoization and Callable Composition: Core Subsystems Use Functools Memoization Decorators

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Core workflow dependency checking, trigger rule evaluations, and task state computations.
- Serialization and deserialization definitions for operators, task groups, and directed acyclic graphs.
- Lifecycle monitoring, job heartbeats, and provider manager registry evaluations.

### Rules

- **R-LM-001** MUST: Core subsystems MUST use functools memoization decorators rather than ad-hoc mutable state structures for in-memory calculation caching and callable manipulation.

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
