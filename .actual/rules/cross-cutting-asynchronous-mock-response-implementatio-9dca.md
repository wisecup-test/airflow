---
version: 2
name: cross-cutting-asynchronous-mock-response-implementatio-9dca
description: "asyncio Concurrency Architecture: Asynchronous Mock Response Implementations Client Protocols"
metadata:
  actual:
    adr_id: 9dca2c66-e605-4033-8e89-0d434d1e3980
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

# asyncio Concurrency Architecture: Asynchronous Mock Response Implementations Client Protocols

Scope: applies to `devel-common/src/sphinx_exts/pagefind_search/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Test utilities and harnesses that invoke asynchronous deferrable operators or triggers
- Mock network client response implementations simulating asynchronous protocols
- Build and indexing extensions executing asynchronous I/O and thread-bridged tasks

### Rules

- **R-PS-001** MUST: Asynchronous mock response implementations for client protocols MUST replicate standard coroutine protocols and expose asynchronous payload resolution methods.

### Verify

- Discover the repository test runner script and execute the test suite targeting asynchronous utilities and deferrable operators.
- Discover the repository linting and static analysis configuration to check for unawaited coroutines and improper event loop invocations.

**Accept when:**

- All asynchronous test cases and deferrable operator trigger evaluations pass without unhandled coroutine warnings.
- Static analysis checks pass with zero unhandled coroutines or improper event loop lifecycle invocations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites executing deferrable operator and mock client test suites
- Verified by: Static analysis linters configured to detect unhandled coroutines and improper loop creation
- Verified by: Peer code review for test utilities and indexing pipeline changes
- Violation handling: Pull requests introducing unhandled coroutines or manual loop lifecycle violations fail automated verification checks.
- Violation handling: Violations identified during code review require refactoring to use standardized runner interfaces prior to merge.
- Exception process: Submit an architectural review request detailing why standard runner functions cannot accommodate the custom event loop lifecycle.
- Exception process: Obtain sign-off from the technical lead and document the lifecycle boundaries in the affected module header.
</enforcement>
