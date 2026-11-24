---
version: 2
name: cross-cutting-modules-within-shared-namespace-not-7f77
description: "Adoption of pkgutil for Namespace Package Protocol Extension: Modules Within Shared Namespace Not Introduce"
metadata:
  actual:
    adr_id: 7f7782fe-9b28-43cb-96fe-b19f24c7127b
    status: accepted
    candidate_group_id: 65ab2eb6-88e1-4e66-8e46-d4c50096acab
    evidence:
      source: deep
  scope:
    root: providers/apache/druid
    paths:
      - src/airflow/**
---

# Adoption of pkgutil for Namespace Package Protocol Extension: Modules Within Shared Namespace Not Introduce

Scope: applies to `providers/apache/druid/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Package initialization modules defining shared root or intermediary provider namespaces.
- Modular provider distributions extending the common public namespace hierarchy.

### Rules

- **R-PEA-001** MUST_NOT: Modules within a shared namespace MUST NOT introduce circular imports or direct cross-provider bindings during namespace initialization.

### Verify

- Discover the project test runner configuration and execute package import verification tests covering namespace resolution.
- Discover the project static analysis and linting suite to verify compliance with namespace initialization conventions.

**Accept when:**

- Namespace package modules successfully resolve and import across all distributed provider boundaries without path collisions.
- Dynamic package path extension passes project import validation suites across supported runtime environments.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks verifying clean imports of all provider packages.
- Verified by: Static analysis linting rules auditing namespace initialization modules.
- Violation handling: Pull requests introducing non-standard namespace initialization logic or heavy imports will be blocked by continuous integration.
- Violation handling: Violations detected in existing modules must be refactored to comply with the standard protocol.
- Exception process: Exceptions require review and approval by the architecture and release engineering maintainers.
- Exception process: Approved exceptions must be documented in the repository architectural exceptions log.
</enforcement>
