---
version: 2
name: cross-cutting-before-implementing-code-that-relies-4e36
description: "Adoption of airflowctl Core Module Architecture: Before Implementing Code That Relies Versioned"
metadata:
  actual:
    adr_id: 4e361c5b-a92e-463a-8a04-481014b18152
    status: accepted
    candidate_group_id: 42c052e8-12a7-48e1-b50d-1f4a4dc19b96
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - docs/**
      - src/airflowctl/ctl/commands/**
---

# Adoption of airflowctl Core Module Architecture: Before Implementing Code That Relies Versioned

Scope: applies to `airflow-ctl/docs/**`, `airflow-ctl/src/airflowctl/ctl/commands/**`.

These rules are ALWAYS ACTIVE for:
- All command modules, client consumers, and documentation configuration scripts interacting with internal package utilities.

### Rules

- **R-LM-001** MUST: Before implementing code that relies on versioned dependencies, the consumer MUST discover the ecosystem lock artifact and verify the exact resolved version.

### Verify

- Discover and execute the repository linter and import validation target to ensure boundary integrity.
- Discover and execute the repository test suite to verify internal package contract compatibility.

**Accept when:**

- All modules consuming the internal package resolve interfaces through the designated namespace hierarchy without circular imports.
- Automated test suites and build checks execute successfully across all entry points invoking the core module.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static import analysis, packaging lint checks, and unit test suites during continuous integration.
- Violation handling: Build failure upon detection of undeclared module imports or direct bypass of established package boundaries.
- Exception process: Submit an architectural change proposal documenting interface constraints and import necessity to the engineering leads.
</enforcement>
