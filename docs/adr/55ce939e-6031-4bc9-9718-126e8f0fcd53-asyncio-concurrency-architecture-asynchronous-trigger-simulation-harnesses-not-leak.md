---
version: 2
name: 55ce939e-6031-4bc9-9718-126e8f0fcd53-asyncio-concurrency-architecture-asynchronous-trigger-simulation-harnesses-not-leak
description: "asyncio Concurrency Architecture: Asynchronous Trigger Simulation Harnesses Not Leak"
metadata:
  actual:
    adr_id: 55ce939e-6031-4bc9-9718-126e8f0fcd53
    status: accepted
    candidate_group_id: d2f8dfa2-9376-4ae3-bf56-81ffb20a9691
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/pagefind_search/**
      - src/tests_common/test_utils/**
---

# asyncio Concurrency Architecture: Asynchronous Trigger Simulation Harnesses Not Leak

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/pagefind_search/**`
- `src/tests_common/test_utils/**`

## Context

- The codebase coordinates asynchronous routines, deferrable operator lifecycles, and non-blocking I/O operations alongside synchronous test suites and build extensions.
- Executing asynchronous coroutines within synchronous harnesses requires explicit lifecycle management and standardized event loop entry points.
- Testing deferrable execution paths demands deterministic simulation of triggers and asynchronous responses without running detached background services.
- Asynchronous file processing and search indexing require managed thread offloading to avoid blocking event loops during disk-heavy phases.

## Problem Statement

Asynchronous operations such as deferrable operator trigger evaluations, mock network client responses, and documentation search indexing cannot execute directly in synchronous environments without standardized event loop coordination. Unstructured concurrency, manual event loop handling, or ad-hoc thread dispatching leads to resource leaks, unhandled coroutines, and flaky test execution across test harnesses and build extensions.

## Decision

- **R-PS-001** MUST_NOT: Asynchronous trigger simulation harnesses MUST_NOT leak pending coroutines or uncollected task exceptions across test execution boundaries.

## Policy Block

In scope:

- Test utilities and harnesses that invoke asynchronous deferrable operators or triggers
- Mock network client response implementations simulating asynchronous protocols
- Build and indexing extensions executing asynchronous I/O and thread-bridged tasks

Out of scope:

- Purely synchronous utility functions without coroutine invocation
- Standard blocking unit tests that do not evaluate asynchronous triggers or clients

Exceptions:

- **EXC-17-001** An active event loop is already managing execution in a persistent asynchronous daemon context
  Approval: Architecture Review Board
  Documentation: Document the hosting event loop lifecycle and task scheduling boundary in the module header

## Rationale

- Standardizing on asyncio.run for synchronous-to-asynchronous boundaries prevents leaked event loops and dangling tasks across test runs.
- Providing protocol-compliant asynchronous mock clients allows deferrable execution paths to be validated deterministically without real network endpoints.
- Bridging synchronous filesystem operations to worker threads preserves event loop responsiveness during multi-file indexing workflows.

## Consequences

Positive:

- Deterministic lifecycle isolation and automated cleanup of event loops during test execution
- Standardized patterns for evaluating deferrable operator triggers and simulating asynchronous responses
- Non-blocking execution for documentation indexing pipelines with thread-bridged filesystem operations

Negative:

- Per-invocation event loop creation and teardown overhead when executing sequential tests via synchronous-to-asynchronous entry points
- Dual cognitive overhead of managing both synchronous execution models and asynchronous coroutine protocols within the same codebase

## Alternatives

- Manual event loop management via low-level loop instantiation and manual loop teardown calls (rejected)
  Rejected because: Manual loop management risks unclosed event loops, hanging tasks, and cross-test state pollution across test harnesses
  When valid: Long-running daemon processes that require persistent custom event loop lifecycles and dedicated OS signal handlers
- Thread-only concurrency model using blocking worker thread pools for all background operations (rejected)
  Rejected because: Fails to validate or execute native coroutines, asynchronous deferrable operator triggers, and non-blocking network protocols
  When valid: Environments where all dependencies are strictly synchronous and coroutines are absent

## Risks

- Accidental blocking operations inside coroutines stalling the event loop
  Mitigation: Offload blocking synchronous calls to dedicated worker threads via asynchronous thread execution bridges
  Owner: Engineering Team
- Uncollected exceptions in deferred triggers causing silent failures in test harnesses
  Mitigation: Enforce explicit event collection and assertion within deferrable test runner utilities
  Owner: Quality Assurance Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Wrap deferrable operator execution tests in dedicated runner functions that capture generated trigger events through asyncio.run.
- Implement mock network responses with coroutine-compatible payload methods to ensure interface compatibility with asynchronous HTTP clients.

## Continuation Context

Verify commands:

- Discover the repository test runner script and execute the test suite targeting asynchronous utilities and deferrable operators.
- Discover the repository linting and static analysis configuration to check for unawaited coroutines and improper event loop invocations.

Accept when:

- All asynchronous test cases and deferrable operator trigger evaluations pass without unhandled coroutine warnings.
- Static analysis checks pass with zero unhandled coroutines or improper event loop lifecycle invocations.

## Enforcement

- Verified by: Automated continuous integration test suites executing deferrable operator and mock client test suites
- Verified by: Static analysis linters configured to detect unhandled coroutines and improper loop creation
- Verified by: Peer code review for test utilities and indexing pipeline changes
- Violation handling: Pull requests introducing unhandled coroutines or manual loop lifecycle violations fail automated verification checks.
- Violation handling: Violations identified during code review require refactoring to use standardized runner interfaces prior to merge.
- Exception process: Submit an architectural review request detailing why standard runner functions cannot accommodate the custom event loop lifecycle.
- Exception process: Obtain sign-off from the technical lead and document the lifecycle boundaries in the affected module header.
