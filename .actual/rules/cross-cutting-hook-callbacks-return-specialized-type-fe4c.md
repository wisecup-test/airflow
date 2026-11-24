---
version: 2
name: cross-cutting-hook-callbacks-return-specialized-type-fe4c
description: "Adoption of mypy.plugin Extension Hooks for Custom Static Type Checking: Hook Callbacks Return Specialized Type Representations"
metadata:
  actual:
    adr_id: fe4cb8a4-93d8-4f2d-8c9e-f7b587e759a3
    status: accepted
    candidate_group_id: 31113f0d-d227-40bd-8bde-4ea961fa1bb3
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - mypy/src/airflow_mypy/plugins/**
---

# Adoption of mypy.plugin Extension Hooks for Custom Static Type Checking: Hook Callbacks Return Specialized Type Representations

Scope: applies to `dev/mypy/src/airflow_mypy/plugins/**`.

These rules are ALWAYS ACTIVE for:
- Type checking plugin modules extending static analysis for framework-specific DSLs, decorators, and deferred execution arguments.

### Rules

- **R-LM-001** SHOULD: Hook callbacks SHOULD return specialized type representations from mypy.types to preserve type safety rather than falling back to unconstrained types unless runtime evaluation cannot be statically modeled.

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
