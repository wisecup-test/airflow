---
version: 2
name: cross-cutting-developer-tooling-performance-suites-met-1f26
description: "Standard Library Datetime Module for Temporal Arithmetic and Date Validation: Developer Tooling Performance Suites Metadata Extraction"
metadata:
  actual:
    adr_id: 1f266cf2-68d9-4e0b-8f52-ebc1c6fe3ebe
    status: accepted
    candidate_group_id: 9efdc7f4-3af4-48dd-bae0-bb5409be6b73
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/dags/**
      - breeze/src/airflow_breeze/**
      - registry/**
---

# Standard Library Datetime Module for Temporal Arithmetic and Date Validation: Developer Tooling Performance Suites Metadata Extraction

Scope: applies to `dev/airflow_perf/dags/**`, `dev/breeze/src/airflow_breeze/**`, `dev/registry/**`.

These rules are ALWAYS ACTIVE for:
- Components handling date validation, duration parsing, scheduling deltas, and timestamp extraction within developer tooling and utility modules

### Rules

- **R-LM-001** MUST: Developer tooling, performance suites, and metadata extraction modules MUST use the standard library datetime module for temporal arithmetic, date validation, and duration representation.

### Verify

- # Discover test runner configuration and execute test suites covering date validation
- # Discover and run linting scripts to verify compliance with datetime import constraints

**Accept when:**

- All date validation and duration calculation test suites execute and pass without errors
- Modules handling dates and time intervals conform to datetime and timedelta interfaces

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites executed during continuous integration
- Verified by: Peer code review during pull request evaluation
- Violation handling: Pull requests containing unapproved third-party temporal dependencies or invalid date parsing logic must be blocked until remediated
- Exception process: Submit an architectural review request detailing why standard library datetime constructs are insufficient for the specific use case
</enforcement>
