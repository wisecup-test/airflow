---
version: 2
name: cross-cutting-when-data-access-operations-interact-9b15
description: "Standard Collection Set In-Memory Accumulation: When Data Access Operations Interact Versioned"
metadata:
  actual:
    adr_id: 9b15306d-ef5b-4b9a-9cb0-f64ce3d795ee
    status: accepted
    candidate_group_id: 9ebf4ac4-65ae-4795-a3dc-1bb328387a3f
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
      - ide_setup/**
      - registry/**
      - stats/**
      - validate_version_added_fields_in_config.py
---

# Standard Collection Set In-Memory Accumulation: When Data Access Operations Interact Versioned

Scope: applies to `dev/breeze/src/airflow_breeze/**`, `dev/ide_setup/**`, `dev/registry/**`, `dev/stats/**`, `dev/validate_version_added_fields_in_config.py`.

These rules are ALWAYS ACTIVE for:
- Standalone maintenance automation scripts
- Local developer tooling and build utility modules

### Rules

- **R-DAP-001** MUST: When data access operations interact with versioned dependencies or external client libraries, consumers MUST resolve the exact locked versions from repository resolution artifacts before invoking access routines.

### Verify

- Discover and run the project static analysis and test suite using the project-configured execution scripts.
- Execute repository test suites targeting maintenance automation and utility modules to confirm deterministic data collection behavior.

**Accept when:**

- Static verification passes without reporting unhandled collection mutation or data access errors.
- Automated tests confirm that in-memory collections correctly isolate and deduplicate elements across execution runs.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Static code analysis and linter checks run during continuous integration.
- Verified by: Peer code review for changes in maintenance and build automation routines.
- Violation handling: Pull requests introducing persistent storage dependencies for transient collection tasks will be requested to use in-memory collections.
- Exception process: Exceptions require review by the engineering team when durable storage or distributed coordination is strictly required.
</enforcement>
