---
version: 2
name: cross-cutting-diagram-implementations-utilize-structur-1f2b
description: "Adoption of diagrams Library for Declarative Architecture Visualizations: Diagram Implementations Utilize Structured Client Provider"
metadata:
  actual:
    adr_id: 1f2bd44b-85ee-4f4d-bef4-34e72153f0b3
    status: accepted
    candidate_group_id: 985b21dc-cbfe-4ee0-9590-b9e0d7b9dd2c
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - docs/**
      - hatch_build.py
---

# Adoption of diagrams Library for Declarative Architecture Visualizations: Diagram Implementations Utilize Structured Client Provider

Scope: applies to `airflow-core/docs/**`, `airflow-core/hatch_build.py`.

These rules are ALWAYS ACTIVE for:
- All system architecture overview diagrams, subsystem interaction diagrams, and lifecycle topology documentation assets.

### Rules

- **R-PEA-001** SHOULD: Diagram implementations SHOULD utilize structured client and provider node representations from the diagrams library to reflect architectural boundaries accurately.

### Verify

- Discover the documentation build invocation in the repository manifest and execute it to confirm diagram generation scripts run successfully.
- Inspect the project task runner configuration to locate and execute the diagram artifact verification routines.

**Accept when:**

- All architecture diagram generation scripts execute without error and produce expected visual output artifacts.
- Generated architecture diagrams reflect current subsystem boundaries and component topologies without manual intervention.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration documentation build workflows validating diagram compilation.
- Verified by: Peer code review on pull requests modifying architectural documentation assets.
- Violation handling: Pull requests adding un-sourced binary image diagrams or failing diagram scripts are blocked until programmatic definitions are supplied and pass verification.
- Exception process: Exceptions for proprietary visual assets or third-party logos require architectural review approval and documentation in the documentation metadata registry.
</enforcement>
