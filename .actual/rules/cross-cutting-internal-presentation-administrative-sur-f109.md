---
version: 2
name: cross-cutting-internal-presentation-administrative-sur-f109
description: "Adoption of airflowctl Core Module Architecture: Internal Presentation Administrative Surfaces Import Domain"
metadata:
  actual:
    adr_id: f109217d-ffab-41ea-8d7d-a13685c109a9
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

# Adoption of airflowctl Core Module Architecture: Internal Presentation Administrative Surfaces Import Domain

Scope: applies to `airflow-ctl/docs/**`, `airflow-ctl/src/airflowctl/ctl/commands/**`.

These rules are ALWAYS ACTIVE for:
- All command modules, client consumers, and documentation configuration scripts interacting with internal package utilities.

### Rules

- **R-LM-001** MUST: Internal presentation and administrative surfaces MUST import domain operations and API interaction abstractions exclusively through the central airflowctl package namespace.

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
