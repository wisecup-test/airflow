---
version: 2
name: cross-cutting-engineers-implementing-extending-modules-a1c7
description: "Adoption of logging Module with Hierarchical getLogger Namespace: Engineers Implementing Extending Modules Inspect Repository"
metadata:
  actual:
    adr_id: a1c7128e-363c-4f5d-8a18-6543bbc7589e
    status: accepted
    candidate_group_id: 88a7d30c-cdac-4133-af32-d8bc5ca3dd47
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - assign_cherry_picked_prs_with_milestone.py
      - prepare_bulk_issues.py
      - stats/**
---

# Adoption of logging Module with Hierarchical getLogger Namespace: Engineers Implementing Extending Modules Inspect Repository

Scope: applies to `dev/assign_cherry_picked_prs_with_milestone.py`, `dev/prepare_bulk_issues.py`, `dev/stats/**`.

These rules are ALWAYS ACTIVE for:
- Developer utility scripts, maintenance tasks, and data extraction modules executing in the project environment.

### Rules

- **R-LM-001** MUST: Engineers implementing or extending modules MUST inspect the repository dependency manifest and lock artifacts to verify runtime environment constraints and ensure compatibility with the standard logging API.

### Verify

- Discover project static analysis commands from repository manifests and run linting against utility modules.
- Discover test runner execution scripts from project configuration and run test suite verification.

**Accept when:**

- Static analysis checks complete with zero errors regarding logger naming or direct root logger calls.
- All developer utility tests pass with expected log events captured by standard logging fixtures without uncaptured standard error leakage.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated linting and static analysis checks executed during continuous integration workflows.
- Verified by: Peer code review verification during pull request inspection.
- Violation handling: Pull requests containing direct root logger calls or standard output diagnostic prints will fail automated checks and require remediation prior to merge.
- Exception process: Submit an architectural review request detailing technical constraints preventing standard logger hierarchy usage, requiring approval from maintainers.
</enforcement>
