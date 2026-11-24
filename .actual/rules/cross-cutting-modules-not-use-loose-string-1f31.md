---
version: 2
name: cross-cutting-modules-not-use-loose-string-1f31
description: "Python Standard Library enum Module: Modules Not Use Loose String Literals"
metadata:
  actual:
    adr_id: 1f31b124-1c4a-4840-b205-33e198739e34
    status: accepted
    candidate_group_id: afaa3a5c-5c91-4b30-acfe-aeb036e7cec4
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/dags/**
      - breeze/src/airflow_breeze/**
---

# Python Standard Library enum Module: Modules Not Use Loose String Literals

Scope: applies to `dev/airflow_perf/dags/**`, `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- Categorical domain definitions across development tooling and script utilities.
- Shared constant collections representing finite, discrete states or attributes.

### Rules

- **R-LM-001** MUST_NOT: Modules MUST NOT use loose string literals or unbound constants where an enumeration domain has been established.

### Verify

- Discover the project static type checker and run verification across the codebase to ensure valid enumeration member usage.
- Discover the repository test runner and execute the test suite to confirm consistent enumeration evaluation.

**Accept when:**

- Static analysis validates that all enumeration references correspond to declared members without type errors.
- The project test suite executes successfully with zero failures originating from enumeration comparisons or lookups.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks in continuous integration pipelines.
- Verified by: Peer review during code contribution workflows.
- Violation handling: Pull requests introducing loose literal strings for established enumeration domains are blocked until refactored.
- Violation handling: Static analysis type errors cause continuous integration build failure.
- Exception process: Exceptions require documented justification in the pull request detailing why a primitive representation is mandatory at an external interface.
</enforcement>
