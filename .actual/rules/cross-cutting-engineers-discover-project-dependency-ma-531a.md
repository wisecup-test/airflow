---
version: 2
name: cross-cutting-engineers-discover-project-dependency-ma-531a
description: "Adopt github.com/stretchr/testify/mock for API Client Mocking: Engineers Discover Project Dependency Manifest Resolve"
metadata:
  actual:
    adr_id: 531a4cfe-feef-4cd9-ad3f-263053b4f462
    status: accepted
    candidate_group_id: af046700-1fff-4407-a1d4-fe196133d531
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/api/mocks/**
---

# Adopt github.com/stretchr/testify/mock for API Client Mocking: Engineers Discover Project Dependency Manifest Resolve

Scope: applies to `go-sdk/pkg/api/mocks/**`.

These rules are ALWAYS ACTIVE for:
- Unit and consumer test suites defining or using test doubles for API client interfaces.
- Mock client implementations within client SDK packages.

### Rules

- **R-LM-001** MUST: Engineers MUST discover the project dependency manifest and resolve the exact locked version of github.com/stretchr/testify/mock before creating or modifying client mock implementations.

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
