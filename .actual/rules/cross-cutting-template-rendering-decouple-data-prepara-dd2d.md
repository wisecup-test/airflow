---
version: 2
name: cross-cutting-template-rendering-decouple-data-prepara-dd2d
description: "Jinja2 Template Engine Adoption: Template Rendering Decouple Data Preparation Presentation"
metadata:
  actual:
    adr_id: dd2d107c-6272-481a-a92c-ec29754d97cd
    status: accepted
    candidate_group_id: 3f9abf65-5a3d-4617-8145-8fe94f291554
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
---

# Jinja2 Template Engine Adoption: Template Rendering Decouple Data Preparation Presentation

Scope: applies to `devel-common/src/sphinx_exts/**`.

These rules are ALWAYS ACTIVE for:
- Dynamic documentation generation scripts and documentation builder extensions
- Modules rendering structured reference tables, indexes, or HTML components for documentation

### Rules

- **R-LM-001** MUST: Template rendering MUST decouple data preparation from presentation logic by passing structured data dictionaries to template render calls rather than composing markup within source files.

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
