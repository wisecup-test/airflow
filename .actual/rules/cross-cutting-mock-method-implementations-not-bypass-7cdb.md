---
version: 2
name: cross-cutting-mock-method-implementations-not-bypass-7cdb
description: "Adopt github.com/stretchr/testify/mock for API Client Mocking: Mock Method Implementations Not Bypass Argument"
metadata:
  actual:
    adr_id: 7cdbeeef-5616-4c77-aefb-dbd8a58069d3
    status: accepted
    candidate_group_id: af046700-1fff-4407-a1d4-fe196133d531
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/api/mocks/**
---

# Adopt github.com/stretchr/testify/mock for API Client Mocking: Mock Method Implementations Not Bypass Argument

Scope: applies to `go-sdk/pkg/api/mocks/**`.

These rules are ALWAYS ACTIVE for:
- Unit and consumer test suites defining or using test doubles for API client interfaces.
- Mock client implementations within client SDK packages.

### Rules

- **R-LM-001** MUST_NOT: Mock method implementations MUST NOT bypass argument recording or return value extraction via out-of-band global state.

### Verify

- Discover the test runner invocation script from the project configuration and execute the unit test suite targeting mock package implementations.
- Execute the project verification target to evaluate mock contract conformity and assertion validation.

**Accept when:**

- All client test suites executing mock expectations pass with zero unhandled panics or assertion failures.
- Mock implementations successfully satisfy their corresponding client contract interfaces without interface drift.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test runs executing unit test suites.
- Verified by: Static analysis and interface compliance assertions during compilation.
- Verified by: Peer review of pull requests introducing or modifying API client contracts.
- Violation handling: Test failure in continuous integration if mock assertions or expectations fail.
- Violation handling: Rejection of pull requests introducing hand-rolled or unstructured mock doubles for public client interfaces.
- Exception process: Submit an architecture review request documenting why interface mocking with the adopted library does not satisfy the testing requirements.
</enforcement>
