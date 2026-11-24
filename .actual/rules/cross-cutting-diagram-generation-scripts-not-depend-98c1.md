---
version: 2
name: cross-cutting-diagram-generation-scripts-not-depend-98c1
description: "Adoption of diagrams Library for Declarative Architecture Visualizations: Diagram Generation Scripts Not Depend Live"
metadata:
  actual:
    adr_id: 98c1ff8f-ab67-42e7-be00-271c9d72a128
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

# Adoption of diagrams Library for Declarative Architecture Visualizations: Diagram Generation Scripts Not Depend Live

Scope: applies to `airflow-core/docs/**`, `airflow-core/hatch_build.py`.

These rules are ALWAYS ACTIVE for:
- All system architecture overview diagrams, subsystem interaction diagrams, and lifecycle topology documentation assets.

### Rules

- **R-PEA-001** MUST_NOT: Diagram generation scripts MUST NOT depend on live network connections or running cluster environments during artifact generation.

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
