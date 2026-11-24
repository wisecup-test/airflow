---
version: 2
name: cross-cutting-documentation-generation-modules-extensi-0e4a
description: "Jinja2 Template Engine Adoption: Documentation Generation Modules Extensions Adopt Jinja2"
metadata:
  actual:
    adr_id: 0e4aa063-ce2c-43cf-a173-8e9a48a4b2ad
    status: accepted
    candidate_group_id: 3f9abf65-5a3d-4617-8145-8fe94f291554
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
---

# Jinja2 Template Engine Adoption: Documentation Generation Modules Extensions Adopt Jinja2

Scope: applies to `devel-common/src/sphinx_exts/**`.

These rules are ALWAYS ACTIVE for:
- Dynamic documentation generation scripts and documentation builder extensions
- Modules rendering structured reference tables, indexes, or HTML components for documentation

### Rules

- **R-LM-001** MUST: Documentation generation modules and extensions MUST adopt Jinja2 as the standard templating engine for all dynamic markup and document synthesis.

### Verify

- Discover and run the repository documentation build verification suite to confirm templates render successfully without syntax or context errors.
- Discover and execute the project linter and test runner targeting documentation extension modules.

**Accept when:**

- All documentation generation modules render target output files without template syntax or missing variable errors.
- Automated tests for documentation extensions pass verification with full coverage of template rendering paths.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks running documentation generation suites.
- Verified by: Peer code review for changes to documentation extensions and build scripts.
- Violation handling: Pull requests containing hardcoded HTML or multi-line markup strings in documentation extensions are blocked pending template refactoring.
- Exception process: Exceptions must be documented with an architectural note and approved by documentation maintainers.
</enforcement>
