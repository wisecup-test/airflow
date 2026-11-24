---
version: 2
name: cross-cutting-developers-discover-project-dependency-m-66a0
description: "Jinja2 Template Engine Adoption: Developers Discover Project Dependency Manifest Lock"
metadata:
  actual:
    adr_id: 66a0ea02-5cf1-43f6-ad32-faecfc1ca2f5
    status: accepted
    candidate_group_id: 3f9abf65-5a3d-4617-8145-8fe94f291554
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
---

# Jinja2 Template Engine Adoption: Developers Discover Project Dependency Manifest Lock

Scope: applies to `devel-common/src/sphinx_exts/**`.

These rules are ALWAYS ACTIVE for:
- Dynamic documentation generation scripts and documentation builder extensions
- Modules rendering structured reference tables, indexes, or HTML components for documentation

### Rules

- **R-LM-001** MUST: Developers MUST discover the project dependency manifest and lock file to verify the exact resolved version of Jinja2 before calling template APIs.

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
