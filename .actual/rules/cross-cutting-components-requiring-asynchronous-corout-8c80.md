---
version: 2
name: cross-cutting-components-requiring-asynchronous-corout-8c80
description: "asyncio Coroutine Lifecycle and Execution Management: Components Requiring Asynchronous Coroutine Execution Synchronous"
metadata:
  actual:
    adr_id: 8c807920-cf97-4f24-ad52-b5b8eae17f21
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

# asyncio Coroutine Lifecycle and Execution Management: Components Requiring Asynchronous Coroutine Execution Synchronous

Scope: applies to `devel-common/src/sphinx_exts/pagefind_search/**`, `devel-common/src/tests_common/test_utils/operators/**`.

These rules are ALWAYS ACTIVE for:
- All shared developer tooling modules, test utilities invoking deferrable components, and documentation build extensions executing coroutines.

### Rules

- **R-LM-001** MUST: Components requiring asynchronous coroutine execution from synchronous contexts MUST adopt asyncio as the standard execution runtime.

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
