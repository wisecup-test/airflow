---
version: 2
name: cross-cutting-mock-methods-support-dynamic-return-0b9d
description: "Interface Mocking with testify/mock for SDK API Clients: Mock Methods Support Dynamic Return Functions"
metadata:
  actual:
    adr_id: 0b9dac11-ce3d-458d-b5c1-3876cd19d555
    status: accepted
    candidate_group_id: 121e56e3-8383-4134-b341-867d020ef7cc
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/api/mocks/**
---

# Interface Mocking with testify/mock for SDK API Clients: Mock Methods Support Dynamic Return Functions

Scope: applies to `go-sdk/pkg/api/mocks/**`.

These rules are ALWAYS ACTIVE for:
- Unit tests simulating SDK client interactions across service boundaries.
- Test double definitions for public client interfaces.

### Rules

- **R-TS-001** SHOULD: Mock methods SHOULD support dynamic return functions evaluated via type assertion before falling back to static cast return values.

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
