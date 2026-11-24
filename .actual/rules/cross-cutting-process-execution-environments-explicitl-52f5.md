---
version: 2
name: cross-cutting-process-execution-environments-explicitl-52f5
description: "Subprocess Module Adoption for External Process Execution: Process Execution Environments Explicitly Isolate Sanitize"
metadata:
  actual:
    adr_id: 52f5df99-8ebf-44bf-99ce-c146c58c3f1b
    status: accepted
    candidate_group_id: a726c4f0-4561-4f9b-ab7e-fe47bbbe758f
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - assign_cherry_picked_prs_with_milestone.py
      - breeze/src/airflow_breeze/**
      - prune_old_dirs.py
      - registry/**
      - retag_docker_images.py
---

# Subprocess Module Adoption for External Process Execution: Process Execution Environments Explicitly Isolate Sanitize

Scope: applies to `dev/assign_cherry_picked_prs_with_milestone.py`, `dev/breeze/src/airflow_breeze/**`, `dev/prune_old_dirs.py`, `dev/registry/**`, `dev/retag_docker_images.py`.

These rules are ALWAYS ACTIVE for:
- All automation scripts, release management modules, and developer tooling invoking external system executables.

### Rules

- **R-LM-001** SHOULD: Process execution environments SHOULD explicitly isolate or sanitize execution environment dictionaries when invoking sensitive system operations.

### Verify

- # Discover the project test runner and execute the test suite covering process execution and release validation modules
- # Discover and run the project static analysis linter to verify process execution safety rules
- # Discover and execute integration tests to ensure external process wrappers handle non-zero exit codes correctly

**Accept when:**

- All unit and integration test suites covering process execution modules pass without unhandled process exceptions.
- Static analysis verification confirms zero unescaped shell executions or uninspected return codes across automation modules.
- Process execution helpers correctly capture output streams and report non-zero return codes.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites
- Verified by: Static analysis and linter checks
- Verified by: Peer code review for automation and tooling pull requests
- Violation handling: Continuous integration failure upon detection of non-compliant process execution or lint violations
- Violation handling: Mandatory pull request remediation prior to merge
- Exception process: Submit an architectural review request documenting the specific requirement for non-standard process execution.
- Exception process: Approval required from development tooling maintainers.
</enforcement>
