---
version: 2
name: cross-cutting-syntax-node-attribute-parameter-inspecti-0a45
description: "ast Module for Static Code Analysis and Metadata Extraction: Syntax Node Attribute Parameter Inspection Resolve"
metadata:
  actual:
    adr_id: 0a45f2b2-f554-4b56-9d5b-d01a66cbe236
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

# ast Module for Static Code Analysis and Metadata Extraction: Syntax Node Attribute Parameter Inspection Resolve

Scope: applies to `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Documentation generation extensions that collect metadata about project classes, functions, and interfaces.
- Test utility modules that verify source file imports and structural contracts.
- Build-time and development-time source analysis utilities.

### Rules

- **R-LM-001** SHOULD: Syntax node attribute and parameter inspection SHOULD resolve literal expressions via dedicated AST evaluation routines rather than dynamic evaluation functions.

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
