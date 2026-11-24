---
version: 2
name: cross-cutting-consumer-inspect-repository-lock-artifac-4290
description: "Akeyless SDK Client Integration: Consumer Inspect Repository Lock Artifact Resolve"
metadata:
  actual:
    adr_id: 42905ff3-99a3-4e3f-ba5d-ab4e7dbb3988
    status: accepted
    candidate_group_id: d4289d50-4e37-4e1c-9bde-4de6e4bca425
    evidence:
      source: deep
  scope:
    root: providers/akeyless
    paths:
      - src/airflow/**
---

# Akeyless SDK Client Integration: Consumer Inspect Repository Lock Artifact Resolve

Scope: applies to `providers/akeyless/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Integration components and backend providers interacting with the Akeyless Vault Platform for secret and variable retrieval.
- Connection management routines mediating external vault authentication and session caching.

### Rules

- **R-PEA-001** MUST: The consumer MUST inspect the repository lock artifact to resolve the exact dependency version of the akeyless SDK before implementation.

### Verify

- Discover the repository test runner from the project manifest and execute the test suite covering vault hook and secret backend modules.
- Discover the static analysis tool configured in the repository and run type and style checks across the provider integration code.

**Accept when:**

- All unit and integration tests covering vault client authentication and secret retrieval execute successfully without errors.
- Static type analysis passes with zero type errors across the provider hook and backend modules.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration test execution on pull requests affecting provider integration components.
- Verified by: Automated static analysis and code review verification of hook and backend abstractions.
- Violation handling: Pull requests introducing direct HTTP vault calls or unencapsulated client creation will be blocked during code review.
- Violation handling: Build failures triggered by missing lock version verification or failing integration tests block merging.
- Exception process: Exceptions require approval from architectural maintainers with documented justification for custom protocol access.
</enforcement>
