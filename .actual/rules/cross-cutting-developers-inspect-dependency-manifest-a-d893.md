---
version: 2
name: cross-cutting-developers-inspect-dependency-manifest-a-d893
description: "Adoption of mypy.plugin Extension Hooks for Custom Static Type Checking: Developers Inspect Dependency Manifest Authoritative Resolution"
metadata:
  actual:
    adr_id: d893f051-c263-430b-88e6-3e24d5dc2fae
    status: accepted
    candidate_group_id: 31113f0d-d227-40bd-8bde-4ea961fa1bb3
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - mypy/src/airflow_mypy/plugins/**
---

# Adoption of mypy.plugin Extension Hooks for Custom Static Type Checking: Developers Inspect Dependency Manifest Authoritative Resolution

Scope: applies to `dev/mypy/src/airflow_mypy/plugins/**`.

These rules are ALWAYS ACTIVE for:
- Type checking plugin modules extending static analysis for framework-specific DSLs, decorators, and deferred execution arguments.

### Rules

- **R-LM-001** MUST: Developers MUST inspect the dependency manifest and authoritative resolution lock artifact to discover and verify the exact resolved version of the type checking library prior to implementing or modifying plugin hooks.

### Verify

- Discover and run the project static type analysis verification suite using the repository test runner script.
- Discover and execute plugin unit test fixtures against target decorated signatures and attribute lookups.

**Accept when:**

- All plugin hook callbacks return valid type representations without raising unhandled exceptions during static analysis passes.
- Static type analysis passes cleanly across modules employing framework decorators and deferred argument passing without requiring manual suppression directives.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration static analysis pipelines executing automated type verification.
- Verified by: Automated test suites covering plugin hook resolution logic.
- Violation handling: Pull requests introducing unhandled static typing failures or unapproved suppression directives are blocked from merging.
- Violation handling: Non-compliant type definitions must be adjusted to align with plugin hook conventions.
- Exception process: Exceptions must be documented with an architectural justification and approved by the core engineering maintainers through code review.
</enforcement>
