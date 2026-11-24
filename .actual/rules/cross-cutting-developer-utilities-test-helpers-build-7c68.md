---
version: 2
name: cross-cutting-developer-utilities-test-helpers-build-7c68
description: "Adoption of Requests for Synchronous HTTP Client Interactions: Developer Utilities Test Helpers Build Extensions"
metadata:
  actual:
    adr_id: 7c683940-e3f8-469b-83b2-7489fd80a5db
    status: accepted
    candidate_group_id: 07dc8210-bda7-4db3-bc0f-4a896883f0bd
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# Adoption of Requests for Synchronous HTTP Client Interactions: Developer Utilities Test Helpers Build Extensions

Scope: applies to `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Synchronous HTTP client interactions within developer utilities, test helpers, and documentation build extensions.

### Rules

- **R-LM-001** MUST: Developer utilities, test helpers, and build extensions requiring synchronous HTTP communication MUST use requests for external network interactions.

### Verify

- Discover and execute the repository test runner targeting external API client and integration helper test suites.
- Execute the project static analysis and dependency validation check to verify compliance of networking client imports.

**Accept when:**

- All test suites exercising external API clients and documentation helpers pass with expected HTTP response handling.
- Static verification confirms that all synchronous HTTP client calls in developer and test utilities import and use the approved library without unhandled socket errors.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test execution suites verifying API client interactions.
- Verified by: Static analysis rules validating that network client imports conform to approved libraries.
- Verified by: Peer code review during pull request inspection.
- Violation handling: Pull requests introducing unauthorized HTTP client dependencies will be blocked by automated checks.
- Violation handling: Code using unapproved networking patterns must be refactored to use standard session utilities prior to merging.
- Exception process: Submit an architectural exception request detailing technical incompatibilities that prevent synchronous client adoption.
- Exception process: Obtain formal approval from infrastructure maintainers before introducing alternative HTTP transport libraries.
</enforcement>
