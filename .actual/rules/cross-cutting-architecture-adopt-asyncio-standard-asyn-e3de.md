---
version: 2
name: cross-cutting-architecture-adopt-asyncio-standard-asyn-e3de
description: "Asyncio Asynchronous Concurrency Architecture for Trigger and Service Execution: Architecture Adopt Asyncio Standard Asynchronous Concurrency"
metadata:
  actual:
    adr_id: e3deac76-1674-45eb-8cc2-cc34af7870b6
    status: accepted
    candidate_group_id: fea20ff7-73b8-40f5-8dd8-92a2b58b34d5
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Asyncio Asynchronous Concurrency Architecture for Trigger and Service Execution: Architecture Adopt Asyncio Standard Asynchronous Concurrency

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All components implementing asynchronous deferred triggers, shared event streams, and event loop supervisors.
- FastAPI route handlers, authentication managers, and metastore client interfaces executing network or cryptographic I/O.

### Rules

- **R-PS-001** MUST: The architecture MUST adopt asyncio as the standard asynchronous concurrency model for non-blocking I/O operations, deferred task execution, and asynchronous service endpoints.

### Verify

- bash -c 'TEST_RUNNER=$(find . -maxdepth 3 -name "*test*" -type f -executable | head -n 1); [ -n "$TEST_RUNNER" ] && "$TEST_RUNNER" || echo "Discover and run project test runner"'
- bash -c 'TYPE_CHECKER=$(find . -maxdepth 3 -name "*lint*" -type f -executable | head -n 1); [ -n "$TYPE_CHECKER" ] && "$TYPE_CHECKER" || echo "Discover and execute project type checker"'

**Accept when:**

- All asynchronous trigger and service unit tests pass without event loop blocking warnings or unhandled coroutine exceptions.
- Static type checking and linting pass with zero unresolved coroutine, async iterator, or awaitable type errors.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites executing asynchronous unit and integration test suites.
- Verified by: Static analysis linters configured to identify un-awaited coroutines and blocking synchronous operations in async scopes.
- Verified by: Mandatory peer code review for all new trigger definitions, stream handlers, and asynchronous service endpoints.
- Violation handling: Pull requests introducing blocking I/O into event loop contexts or missing asynchronous lifecycle hooks will be rejected.
- Violation handling: Violations identified during code review or automated scans must be refactored to non-blocking APIs or explicitly dispatched to background executor threads.
- Exception process: Exceptions for introducing synchronous operations into asynchronous execution loops must be proposed via an architectural RFC to the core engineering team.
- Exception process: The proposal must demonstrate that no non-blocking equivalent exists and provide an offloading strategy utilizing an executor or worker thread.
</enforcement>
