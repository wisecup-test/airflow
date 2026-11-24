---
version: 2
name: 3a4e827b-1dda-4515-8267-ffcbdbdda24c-asyncio-coroutine-lifecycle-and-execution-management-synchronous-callers-invoking-asynchronous-tasks-invoke
description: "asyncio Coroutine Lifecycle and Execution Management: Synchronous Callers Invoking Asynchronous Tasks Invoke"
metadata:
  actual:
    adr_id: 3a4e827b-1dda-4515-8267-ffcbdbdda24c
    status: accepted
    candidate_group_id: 9bee0d41-d31f-4f74-b1d5-194b68373174
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/pagefind_search/**
      - src/tests_common/test_utils/operators/**
---

# asyncio Coroutine Lifecycle and Execution Management: Synchronous Callers Invoking Asynchronous Tasks Invoke

Date: 2025-05-15
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/pagefind_search/**`
- `src/tests_common/test_utils/operators/**`

## Context

- The codebase maintains shared developer tooling, test harnesses for deferrable operators, and documentation search indexing extensions that interface with asynchronous APIs.
- Synchronous test suites and documentation generation pipelines must invoke asynchronous coroutines without converting entire parent frameworks into asynchronous paradigms.
- Direct inspection of the codebase reveals consistent reliance on asyncio and asyncio.run to manage coroutine lifecycles and event loop state deterministically.

## Problem Statement

Synchronous execution environments such as test runners and documentation builders require a standardized mechanism to execute coroutine-based components and triggers. Without a clear architectural pattern for event loop lifecycle management, modules risk inconsistent loop initialization, leaked background tasks, and execution failures.

## Decision

- **R-LM-001** MUST: Synchronous callers invoking asynchronous tasks MUST invoke coroutines via asyncio.run to ensure discrete event loop instantiation, complete task execution, and deterministic finalization.

## Policy Block

In scope:

- All shared developer tooling modules, test utilities invoking deferrable components, and documentation build extensions executing coroutines.

Out of scope:

- Core runtime server processes that manage persistent long-running daemon event loops.

Exceptions:

- **EXC-001** A caller is already executing inside an active asynchronous event loop context and must await coroutines directly instead of invoking asyncio.run.
  Approval: engineering team
  Documentation: Document the existing active loop context and await the coroutine directly in the module documentation.

## Rationale

- Using asyncio with asyncio.run provides isolated, self-contained event loop lifecycles that prevent state leakage between successive test runs or documentation indexing steps.
- Standardizing on the core asynchronous runtime library eliminates divergent custom loop management strategies while maintaining compatibility with coroutine interfaces.
- The evidence confirms consistent application of asyncio runtime primitives across distinct submodules in the shared developer tooling package.

## Consequences

Positive:

- Deterministic lifecycle management ensures event loops are properly closed and all resources are reclaimed after coroutine execution.
- Synchronous test suites can reliably execute and assert outcomes on deferrable triggers and asynchronous operators without state leakage.
- Consistent concurrency patterns across tooling submodules reduce maintenance overhead and cognitive load.

Negative:

- Instantiating and tearing down a distinct event loop per execution run introduces slight runtime overhead compared to reusing a persistent loop.
- Care must be taken to prevent calling asyncio.run from within an active event loop context to avoid runtime failures.

## Alternatives

- Manual event loop creation and lifecycle handling via low-level event loop policy APIs (rejected)
  Rejected because: Manual loop creation requires explicit handling of closing, exception handlers, and task cancellation, introducing risks of resource leaks and loop pollution across test cases.
  When valid: Valid only in specialized runtime servers where persistent long-running event loops require custom loop policies or custom selectors.
- Purely synchronous blocking implementations for all utility and indexing tasks (rejected)
  Rejected because: Asynchronous operators, deferrable triggers, and concurrent documentation indexers inherently expose coroutine contracts that cannot be executed without an asynchronous runtime.
  When valid: Valid when tasks perform strictly sequential CPU-bound operations with no coroutine-based dependencies.

## Risks

- Nested invocation of asyncio.run within an already running event loop causes runtime errors.
  Mitigation: Ensure synchronous wrappers are only invoked from thread boundaries or synchronous test contexts where no outer loop is currently running.
  Owner: engineering team
- Resource leaks or dangling background tasks failing to terminate before loop closure.
  Mitigation: Structure coroutine routines to await all scheduled child tasks and finalize open resources within asynchronous context managers.
  Owner: engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- When wrapping deferrable tasks or triggers in test runners, ensure input fixtures and mock contexts are instantiated prior to coroutine invocation.
- For documentation indexing or file-intensive operations, combine asynchronous execution with non-blocking file access or thread delegation to prevent blocking the event loop.

## Continuation Context

Verify commands:

- Discover and run the project test suite for operator and trigger execution utilities via the repository test runner.
- Discover and run the repository static analysis and linting scripts to verify asynchronous function calls and loop management compliance.

Accept when:

- All tests invoking asynchronous operators, triggers, and builder extensions pass without unhandled event loop warnings or unclosed coroutine exceptions.
- Static analysis validates that coroutines are properly awaited or invoked through approved execution entry points.

## Enforcement

- Verified by: Automated continuous integration test runs across all test utilities and documentation builder extensions.
- Verified by: Peer code review for pull requests modifying asynchronous execution boundaries or developer tooling.
- Violation handling: Pull requests introducing unmanaged event loop instantiations or unclosed coroutines are blocked until corrected.
- Exception process: Exceptions must be documented with an architectural justification in the calling module and approved by the engineering team during code review.
