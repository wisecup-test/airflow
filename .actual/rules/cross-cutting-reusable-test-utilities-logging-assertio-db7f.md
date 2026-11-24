---
version: 2
name: cross-cutting-reusable-test-utilities-logging-assertio-db7f
description: "Adoption of devel-common Shared Library Module: Reusable Test Utilities Logging Assertion Fixtures"
metadata:
  actual:
    adr_id: db7fbf22-d2aa-4af4-852b-d4b8b6e0c39a
    status: accepted
    candidate_group_id: c6ea98b5-b85e-499a-ae7b-5efeae56426c
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/docs/utils/**
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# Adoption of devel-common Shared Library Module: Reusable Test Utilities Logging Assertion Fixtures

Scope: applies to `devel-common/src/docs/utils/**`, `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Shared test utilities, assertions, and query tracing helpers used across multiple test suites
- Documentation generation extensions, spelling verification routines, and build transforms
- First-party developer tooling and common file loading routines within the workspace

### Rules

- **R-LM-001** MUST: Reusable test utilities, logging assertion fixtures, and file loading functions MUST be exposed via defined public contracts within the shared development module.

### Verify

- Discover the repository dependency manifest and execute the test suite for the shared development module.
- Execute the workspace-wide linter and static analysis checks to verify no circular dependencies exist between packages and the shared module.

**Accept when:**

- All unit and integration tests within the shared development module pass successfully without errors.
- Dependent package test suites and documentation build pipelines successfully import and execute shared utilities without failures.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration pipelines executing workspace test suites across all packages
- Verified by: Static analysis checks detecting duplicated development utilities or unauthorized circular imports
- Verified by: Peer review on pull requests modifying shared development module contracts
- Violation handling: Pull requests introducing duplicated test helpers or bypassing the shared development module are blocked during code review.
- Violation handling: Continuous integration failures resulting from incompatible changes in shared contracts block merging until resolved.
- Exception process: Package authors requiring specialized test utilities not suitable for sharing must document the rationale in the package test directory.
- Exception process: Exceptions must be reviewed and approved by workspace architecture maintainers.
</enforcement>
