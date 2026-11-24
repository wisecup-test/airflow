---
version: 2
name: cross-cutting-regular-expressions-that-execute-repeate-5d65
description: "re Module Pattern Compilation and Structured Token Extraction: Regular Expressions That Execute Repeatedly Across"
metadata:
  actual:
    adr_id: 5d656eb2-2baf-4504-a728-2b13c9e62d13
    status: accepted
    candidate_group_id: f94cb5c1-0a66-42a1-bedc-df55a31c0772
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/dags/**
      - assign_cherry_picked_prs_with_milestone.py
      - breeze/src/airflow_breeze/**
      - chart/**
      - ide_setup/**
      - provider_db_inventory.py
      - react-plugin-tools/**
      - registry/**
      - stats/**
      - update_github_branch_config.py
---

# re Module Pattern Compilation and Structured Token Extraction: Regular Expressions That Execute Repeatedly Across

Scope: applies to `dev/airflow_perf/dags/**`, `dev/assign_cherry_picked_prs_with_milestone.py`, `dev/breeze/src/airflow_breeze/**`, `dev/chart/**`, `dev/ide_setup/**`, `dev/provider_db_inventory.py`, `dev/react-plugin-tools/**`, `dev/registry/**`, `dev/stats/**`, `dev/update_github_branch_config.py`.

These rules are ALWAYS ACTIVE for:
- Developer tooling, build scripts, administrative utilities, and runtime modules performing text parsing, format validation, and structured metadata extraction.

### Rules

- **R-LM-001** MUST: Regular expressions that execute repeatedly or across collections MUST be pre-compiled into pattern objects at module scope using re.compile rather than calling inline module-level evaluation functions.

### Verify

- Discover the project test runner configuration from the repository and execute test suites targeting regular expression parsing utilities.
- Execute the repository code quality and static analysis checks to verify pattern compilation practices and compliance with lint rules.

**Accept when:**

- All unit and integration test suites covering regular expression extraction and parsing functions pass without failure.
- Static analysis checks confirm module-level pre-compilation of regular expression patterns and absence of lint violations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites and static analysis linters.
- Verified by: Peer code review verification during pull request evaluations.
- Violation handling: Automated continuous integration pipeline failures blocking pull request merges.
- Violation handling: Code review revision requests requiring conversion of ad-hoc string slicing or inline regex calls to compiled patterns.
- Exception process: Exceptions must be submitted via formal code review with benchmarking evidence demonstrating necessity and approved by project maintainers.
</enforcement>
