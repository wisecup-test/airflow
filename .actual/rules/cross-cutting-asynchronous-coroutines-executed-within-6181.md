---
version: 2
name: cross-cutting-asynchronous-coroutines-executed-within-6181
description: "Asyncio Asynchronous Concurrency Architecture for Trigger and Service Execution: Asynchronous Coroutines Executed Within Event Loop"
metadata:
  actual:
    adr_id: 6181324c-f74c-4bf3-829a-e9b050d323f5
    status: accepted
    candidate_group_id: fea20ff7-73b8-40f5-8dd8-92a2b58b34d5
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Asyncio Asynchronous Concurrency Architecture for Trigger and Service Execution: Asynchronous Coroutines Executed Within Event Loop

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All components implementing asynchronous deferred triggers, shared event streams, and event loop supervisors.
- FastAPI route handlers, authentication managers, and metastore client interfaces executing network or cryptographic I/O.

### Rules

- **R-PS-001** MUST_NOT: Asynchronous coroutines executed within the event loop MUST NOT execute blocking synchronous I/O or long-running CPU-bound operations directly on the event loop thread.

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
