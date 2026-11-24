---
version: 2
name: cross-cutting-modules-not-emit-log-events-4b27
description: "Standard Logging with Module-Scoped Hierarchy via logging.getLogger(__name__): Modules Not Emit Log Events Through"
metadata:
  actual:
    adr_id: 4b279d68-e207-454c-a278-b7f5a71105e8
    status: accepted
    candidate_group_id: b5e38043-d7e2-4d83-a8f1-5b26783b8a45
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - assign_cherry_picked_prs_with_milestone.py
      - prepare_bulk_issues.py
      - registry/**
      - stats/**
---

# Standard Logging with Module-Scoped Hierarchy via logging.getLogger(__name__): Modules Not Emit Log Events Through

Scope: applies to `dev/assign_cherry_picked_prs_with_milestone.py`, `dev/prepare_bulk_issues.py`, `dev/registry/**`, `dev/stats/**`.

These rules are ALWAYS ACTIVE for:
- Developer tooling, maintenance scripts, and automation utilities that produce diagnostic, operational, or status logs.

### Rules

- **R-L-001** MUST_NOT: Modules MUST NOT emit log events through root logger convenience functions or bind loggers to hardcoded string literals.

### Verify

- Discover and run the repository static analysis and linting validation script to inspect module-level logger instantiations.
- Discover the test runner from repository manifests and execute unit test suites covering operational utilities to verify diagnostic log emission.

**Accept when:**

- Static analysis confirms all operational utility modules instantiate loggers using logging.getLogger(__name__) without unapproved root logging calls.
- Automated tests for operational scripts pass with expected log events captured at configured severity levels.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks in continuous integration workflows
- Verified by: Peer code review for pull requests modifying tooling or operational scripts
- Violation handling: Pull requests containing direct root logging calls or hardcoded logger channels will fail static validation and require remediation prior to merge.
- Exception process: Exceptions must be requested through code review with justification explaining interactive CLI constraints and approved by the tooling maintainer.
</enforcement>
