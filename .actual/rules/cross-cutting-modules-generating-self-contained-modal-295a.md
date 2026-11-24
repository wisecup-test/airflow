---
version: 2
name: cross-cutting-modules-generating-self-contained-modal-295a
description: "Jinja2 Template Engine Adoption: Modules Generating Self Contained Modal Single"
metadata:
  actual:
    adr_id: 295a1eea-03a4-48e3-8b6d-a636a51ff541
    status: accepted
    candidate_group_id: 3f9abf65-5a3d-4617-8145-8fe94f291554
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
---

# Jinja2 Template Engine Adoption: Modules Generating Self Contained Modal Single

Scope: applies to `devel-common/src/sphinx_exts/**`.

These rules are ALWAYS ACTIVE for:
- Dynamic documentation generation scripts and documentation builder extensions
- Modules rendering structured reference tables, indexes, or HTML components for documentation

### Rules

- **R-LM-001** MAY: Modules generating self-contained modal or single-block snippets MAY instantiate standalone Template objects when registering extension assets.

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
