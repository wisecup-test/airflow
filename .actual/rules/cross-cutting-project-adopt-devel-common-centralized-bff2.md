---
version: 2
name: cross-cutting-project-adopt-devel-common-centralized-bff2
description: "Adoption of devel-common Shared Library Module: Project Adopt Devel Common Centralized Internal"
metadata:
  actual:
    adr_id: bff2428f-b91f-40d0-985e-150f81696153
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

# Adoption of devel-common Shared Library Module: Project Adopt Devel Common Centralized Internal

Scope: applies to `devel-common/src/docs/utils/**`, `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Shared test utilities, assertions, and query tracing helpers used across multiple test suites
- Documentation generation extensions, spelling verification routines, and build transforms
- First-party developer tooling and common file loading routines within the workspace

### Rules

- **R-LM-001** MUST: The project MUST adopt devel-common as the centralized internal module for all shared development utilities, test execution helpers, query tracing fixtures, and documentation build extensions across repository packages.

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
