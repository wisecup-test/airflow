---
version: 2
name: cross-cutting-before-authoring-modifying-test-doubles-7bbc
description: "Interface Mocking with testify/mock for SDK API Clients: Before Authoring Modifying Test Doubles Consumer"
metadata:
  actual:
    adr_id: 7bbcc627-80ba-4832-928c-c5a45628a8d5
    status: accepted
    candidate_group_id: 121e56e3-8383-4134-b341-867d020ef7cc
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/api/mocks/**
---

# Interface Mocking with testify/mock for SDK API Clients: Before Authoring Modifying Test Doubles Consumer

Scope: applies to `go-sdk/pkg/api/mocks/**`.

These rules are ALWAYS ACTIVE for:
- Unit tests simulating SDK client interactions across service boundaries.
- Test double definitions for public client interfaces.

### Rules

- **R-TS-001** MUST: Before authoring or modifying test doubles, the consumer MUST inspect the repository dependency manifest and resolution artifact to resolve the exact locked version of the mocking framework.

### Verify

- Discover the repository test runner from project configuration and execute test targets across the client mock packages.
- Discover repository static analysis tools to verify type compatibility between mock implementations and client interfaces.

**Accept when:**

- All unit tests executing against client mock doubles pass expectation assertions without unhandled panics.
- Interface compilation passes confirming all mock methods satisfy client contract signatures.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration test pipelines executing automated unit test suites.
- Verified by: Static analysis and compiler checks verifying interface conformance.
- Violation handling: Build pipeline failure upon compilation error or unfulfilled mock assertion.
- Violation handling: Rejection of pull requests introducing untested client interfaces lacking corresponding test doubles.
- Exception process: Submit an architectural waiver detailing why an interface cannot support standard mocking structures, approved by SDK maintainers.
</enforcement>
