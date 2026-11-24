---
version: 2
name: ee050403-e222-4ddc-8c9d-68a64739ba75-asyncio-asynchronous-concurrency-architecture-for-trigger-and-service-execution-trigger-instances-implement-asynchronous-lifecycle-hooks
description: "Asyncio Asynchronous Concurrency Architecture for Trigger and Service Execution: Trigger Instances Implement Asynchronous Lifecycle Hooks"
metadata:
  actual:
    adr_id: ee050403-e222-4ddc-8c9d-68a64739ba75
    status: accepted
    candidate_group_id: fea20ff7-73b8-40f5-8dd8-92a2b58b34d5
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Asyncio Asynchronous Concurrency Architecture for Trigger and Service Execution: Trigger Instances Implement Asynchronous Lifecycle Hooks

Date: 2025-05-15
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- The system must manage thousands of concurrent deferred tasks, event-driven triggers, and high-frequency API requests without exhausting system resources.
- Traditional thread-per-task concurrency creates unsustainable memory bloat and context-switching overhead under high concurrency.
- Core subsystems including deferred triggers, streaming subscribers, and API security token validators require cooperative, non-blocking execution to maximize I/O throughput.
- Database operations and synchronous ORM queries require architectural isolation to prevent stalling real-time event loops.

## Problem Statement

Managing thousands of long-running deferred operations, event streams, and high-concurrency API endpoints using synchronous multi-threading results in severe memory overhead, thread pool exhaustion, and degraded system responsiveness. The system requires a standardized asynchronous concurrency model that enables high-density cooperative multitasking for I/O-bound polling while strictly separating synchronous database operations from the non-blocking execution loop.

## Decision

- **R-PS-001** MUST: Trigger instances MUST implement asynchronous lifecycle hooks cleanup and on_kill using non-blocking coroutines to ensure orderly release of local resources and timely cancellation of external workloads without blocking runtime termination.

## Policy Block

In scope:

- All components implementing asynchronous deferred triggers, shared event streams, and event loop supervisors.
- FastAPI route handlers, authentication managers, and metastore client interfaces executing network or cryptographic I/O.

Out of scope:

- Synchronous relational database schema migrations, table definitions, and synchronous transaction blocks.
- CPU-intensive data transformation workloads and isolated synchronous worker tasks dispatched to separate runtime environments.

## Rationale

- Asynchronous coroutines coordinated by an event loop allow the runtime to maintain thousands of deferred tasks in memory with negligible resource overhead compared to dedicated operating system threads.
- Separating the asynchronous trigger loop from synchronous database interaction ensures that database latency and connection lockouts do not block event dispatch and heartbeat monitoring.
- Standardizing non-blocking interfaces across authentication, API routing, and state stores ensures end-to-end responsiveness under heavy concurrent load.

## Consequences

Positive:

- Minimal memory footprint and low operating system thread consumption when sustaining thousands of concurrent deferred triggers.
- High throughput and low request latency across FastAPI service endpoints and cryptographic token verification workflows.
- Clean architectural separation between non-blocking event-driven polling and synchronous database persistence layers.

Negative:

- Increased codebase complexity requiring strict adherence to asynchronous paradigms, coroutine lifecycle handling, and non-blocking I/O libraries.
- Operational risk of event loop starvation if third-party integrations or user-defined callables invoke blocking synchronous calls.
- More demanding debugging and tracing requirements across asynchronous generator yields and coroutine task boundaries.

## Alternatives

- Thread-per-trigger or thread-pool concurrency model using standard OS threads (rejected)
  Rejected because: Allocating operating system threads for thousands of long-polling deferred triggers causes excessive memory footprint, context-switching overhead, and kernel thread exhaustion.
  When valid: Valid only for smaller workloads with low concurrency where all integrated client libraries lack asynchronous support.
- Process-based worker isolation for each deferred task execution (rejected)
  Rejected because: Process creation overhead is prohibitive for lightweight polling and prevents efficient in-memory stream sharing, subscriber fan-out, and event deduplication.
  When valid: Valid for heavy CPU-bound workloads or untrusted user code execution requiring strict process-level security boundaries.

## Risks

- Accidental invocation of blocking synchronous network or disk operations inside an asynchronous coroutine stalls the event loop for all concurrent tasks.
  Mitigation: Enforce static analysis checks, code reviews, and runtime loop monitoring to detect and isolate blocking synchronous calls into background threads.
  Owner: Core Architecture Team
- Uncaught exceptions or incomplete task cancellation during trigger execution causing resource leaks or orphaned stream subscriptions.
  Mitigation: Mandate comprehensive error handling within coroutine loops and require asynchronous cleanup handlers during task cancellation and supervisor teardown.
  Owner: Runtime Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Construct all trigger implementations around asynchronous generator methods that yield event objects upon state transition and terminate cleanly.
- Separate synchronous database interaction from asynchronous event loops by handling transactions within dedicated supervisor threads or async session adapters.

## Continuation Context

Verify commands:

- bash -c 'TEST_RUNNER=$(find . -maxdepth 3 -name "*test*" -type f -executable | head -n 1); [ -n "$TEST_RUNNER" ] && "$TEST_RUNNER" || echo "Discover and run project test runner"'
- bash -c 'TYPE_CHECKER=$(find . -maxdepth 3 -name "*lint*" -type f -executable | head -n 1); [ -n "$TYPE_CHECKER" ] && "$TYPE_CHECKER" || echo "Discover and execute project type checker"'

Accept when:

- All asynchronous trigger and service unit tests pass without event loop blocking warnings or unhandled coroutine exceptions.
- Static type checking and linting pass with zero unresolved coroutine, async iterator, or awaitable type errors.

## Enforcement

- Verified by: Automated continuous integration test suites executing asynchronous unit and integration test suites.
- Verified by: Static analysis linters configured to identify un-awaited coroutines and blocking synchronous operations in async scopes.
- Verified by: Mandatory peer code review for all new trigger definitions, stream handlers, and asynchronous service endpoints.
- Violation handling: Pull requests introducing blocking I/O into event loop contexts or missing asynchronous lifecycle hooks will be rejected.
- Violation handling: Violations identified during code review or automated scans must be refactored to non-blocking APIs or explicitly dispatched to background executor threads.
- Exception process: Exceptions for introducing synchronous operations into asynchronous execution loops must be proposed via an architectural RFC to the core engineering team.
- Exception process: The proposal must demonstrate that no non-blocking equivalent exists and provide an offloading strategy utilizing an executor or worker thread.
