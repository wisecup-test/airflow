---
version: 2
name: cross-cutting-before-introducing-extending-logging-fac-46c3
description: "Standard Logging with Module-Scoped Hierarchy via logging.getLogger(__name__): Before Introducing Extending Logging Facilities External"
metadata:
  actual:
    adr_id: 46c322d8-d898-4bf7-8dcc-f1980ef39e4f
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

# Standard Logging with Module-Scoped Hierarchy via logging.getLogger(__name__): Before Introducing Extending Logging Facilities External

Scope: applies to `dev/assign_cherry_picked_prs_with_milestone.py`, `dev/prepare_bulk_issues.py`, `dev/registry/**`, `dev/stats/**`.

These rules are ALWAYS ACTIVE for:
- Developer tooling, maintenance scripts, and automation utilities that produce diagnostic, operational, or status logs.

### Rules

- **R-L-001** MUST: Before introducing or extending logging facilities with external runtime or framework logging extensions, the consumer MUST inspect the project dependency manifest and resolved lock artifact to confirm compatible runtime versions.

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
