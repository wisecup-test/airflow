---
version: 2
name: cross-cutting-source-tree-traversal-metadata-discovery-2c8e
description: "ast Module for Static Code Analysis and Metadata Extraction: Source Tree Traversal Metadata Discovery Use"
metadata:
  actual:
    adr_id: 2c8e40cf-88cf-418c-be90-b0f8f8937a46
    status: accepted
    candidate_group_id: 6dd1586d-7267-4338-b301-9c70ca676031
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# ast Module for Static Code Analysis and Metadata Extraction: Source Tree Traversal Metadata Discovery Use

Scope: applies to `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Documentation generation extensions that collect metadata about project classes, functions, and interfaces.
- Test utility modules that verify source file imports and structural contracts.
- Build-time and development-time source analysis utilities.

### Rules

- **R-LM-001** MUST: Source tree traversal for metadata discovery MUST use ast.walk across target AST nodes to identify target function, class, and import declarations.

### Verify

- Discover the project test runner configuration from the repository manifest and execute the test suite covering documentation extensions and static analysis utilities.
- Discover and run the project code quality and linting verification scripts to confirm static analysis routines adhere to repository conventions.

**Accept when:**

- All test suites validating documentation extensions and test utilities complete with zero failures.
- Static source parsing extracts class metadata, decorators, and imports without raising unhandled syntax node exceptions or triggering runtime module imports.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration test suites validating static analysis utilities and documentation extensions.
- Verified by: Peer code review verifying that tooling and documentation components do not introduce dynamic runtime imports of target modules.
- Violation handling: Pull requests introducing direct runtime imports of target components in documentation extensions or test utilities will be blocked during review.
- Violation handling: Static analysis parsing failures or missing AST node handlers in automated builds will fail continuous integration validation.
- Exception process: Submit an architectural review request detailing why static AST inspection is insufficient for the targeted construct and obtain maintainer approval.
</enforcement>
