---
version: 2
name: cross-cutting-consumers-not-bypass-internal-client-4439
description: "Adoption of airflowctl Core Module Architecture: Consumers Not Bypass Internal Client Abstractions"
metadata:
  actual:
    adr_id: 44393b4f-c237-4506-97fc-a10b20b182f0
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

# Adoption of airflowctl Core Module Architecture: Consumers Not Bypass Internal Client Abstractions

Scope: applies to `airflow-ctl/docs/**`, `airflow-ctl/src/airflowctl/ctl/commands/**`.

These rules are ALWAYS ACTIVE for:
- All command modules, client consumers, and documentation configuration scripts interacting with internal package utilities.

### Rules

- **R-LM-001** MUST_NOT: Consumers MUST_NOT bypass the internal client abstractions to establish ad-hoc communication channels with upstream service endpoints.

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
