---
version: 2
name: cross-cutting-modules-requiring-diagnostic-observabili-4455
description: "Adoption of logging Module with Hierarchical getLogger Namespace: Modules Requiring Diagnostic Observability Adopt Core"
metadata:
  actual:
    adr_id: 445513bd-a862-479b-9b36-efef58c6b262
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

# Adoption of logging Module with Hierarchical getLogger Namespace: Modules Requiring Diagnostic Observability Adopt Core

Scope: applies to `dev/assign_cherry_picked_prs_with_milestone.py`, `dev/prepare_bulk_issues.py`, `dev/stats/**`.

These rules are ALWAYS ACTIVE for:
- Developer utility scripts, maintenance tasks, and data extraction modules executing in the project environment.

### Rules

- **R-LM-001** MUST: Modules requiring diagnostic observability MUST adopt the core logging module and initialize a dedicated logger instance using logging.getLogger(__name__) at module scope.

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
