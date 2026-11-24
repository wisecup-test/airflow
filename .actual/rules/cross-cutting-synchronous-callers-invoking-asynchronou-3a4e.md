---
version: 2
name: cross-cutting-synchronous-callers-invoking-asynchronou-3a4e
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

Scope: applies to `devel-common/src/sphinx_exts/pagefind_search/**`, `devel-common/src/tests_common/test_utils/operators/**`.

These rules are ALWAYS ACTIVE for:
- All shared developer tooling modules, test utilities invoking deferrable components, and documentation build extensions executing coroutines.

### Rules

- **R-LM-001** MUST: Synchronous callers invoking asynchronous tasks MUST invoke coroutines via asyncio.run to ensure discrete event loop instantiation, complete task execution, and deterministic finalization.

### Verify

- Discover and run the project test suite for operator and trigger execution utilities via the repository test runner.
- Discover and run the repository static analysis and linting scripts to verify asynchronous function calls and loop management compliance.

**Accept when:**

- All tests invoking asynchronous operators, triggers, and builder extensions pass without unhandled event loop warnings or unclosed coroutine exceptions.
- Static analysis validates that coroutines are properly awaited or invoked through approved execution entry points.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test runs across all test utilities and documentation builder extensions.
- Verified by: Peer code review for pull requests modifying asynchronous execution boundaries or developer tooling.
- Violation handling: Pull requests introducing unmanaged event loop instantiations or unclosed coroutines are blocked until corrected.
- Exception process: Exceptions must be documented with an architectural justification in the calling module and approved by the engineering team during code review.
</enforcement>
