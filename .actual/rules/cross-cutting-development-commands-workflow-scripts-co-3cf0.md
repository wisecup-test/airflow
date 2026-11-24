---
version: 2
name: cross-cutting-development-commands-workflow-scripts-co-3cf0
description: "Adoption of airflow_breeze Internal Modular Library Architecture: Development Commands Workflow Scripts Consume Shared"
metadata:
  actual:
    adr_id: 3cf0fac5-8b00-40c0-905e-ebb25e572457
    status: accepted
    candidate_group_id: 0252069d-9c54-4db2-bd65-3bce5c826c9f
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - assign_cherry_picked_prs_with_milestone.py
      - backport/**
      - breeze/src/airflow_breeze/**
      - prune_old_dirs.py
      - react-plugin-tools/**
      - registry/**
      - send_email.py
      - update_github_branch_config.py
      - validate_version_added_fields_in_config.py
      - verify_release_calendar.py
---

# Adoption of airflow_breeze Internal Modular Library Architecture: Development Commands Workflow Scripts Consume Shared

Scope: applies to `dev/assign_cherry_picked_prs_with_milestone.py`, `dev/backport/**`, `dev/breeze/src/airflow_breeze/**`, `dev/prune_old_dirs.py`, `dev/react-plugin-tools/**`, `dev/registry/**`, `dev/send_email.py`, `dev/update_github_branch_config.py`, `dev/validate_version_added_fields_in_config.py`, `dev/verify_release_calendar.py`.

These rules are ALWAYS ACTIVE for:
- All development orchestration modules, release management commands, and utility routines contained within the repository developer tooling package.
- Shared automation helpers responsible for environment setup, configuration caching, and command-line execution interfaces.

### Rules

- **R-LM-001** MUST: Development commands and workflow scripts MUST consume shared utility contracts provided by the internal library for console messaging, user confirmation, and execution caching rather than reimplementing ad-hoc routines.

### Verify

- Discover the project test suite runner and execute unit tests covering the internal developer tooling package.
- Discover and run static type checkers and linter suites against the internal tooling package directories.

**Accept when:**

- All internal developer tooling modules pass repository static analysis and linting checks without violations.
- Unit and integration tests for command routing, parameter models, and cache utilities pass successfully.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks executing repository test and linting suites.
- Verified by: Pull request code reviews ensuring new developer automation conforms to the internal module structure.
- Violation handling: Pull requests introducing uncoordinated standalone automation scripts or violating internal module boundaries are blocked from merging.
- Exception process: Exceptions require approval from developer tooling maintainers and must include documented justification for external script placement.
</enforcement>
