---
version: 2
name: cross-cutting-diagram-definitions-encapsulate-topology-0040
description: "Adoption of diagrams Library for Declarative Architecture Visualizations: Diagram Definitions Encapsulate Topology Construction Within"
metadata:
  actual:
    adr_id: 00406ca8-94c2-4c62-a6d5-b90e0719f17f
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

# Adoption of diagrams Library for Declarative Architecture Visualizations: Diagram Definitions Encapsulate Topology Construction Within

Scope: applies to `airflow-core/docs/**`, `airflow-core/hatch_build.py`.

These rules are ALWAYS ACTIVE for:
- All system architecture overview diagrams, subsystem interaction diagrams, and lifecycle topology documentation assets.

### Rules

- **R-PEA-001** MUST: Diagram definitions MUST encapsulate topology construction within an exported generation function that manages output file resolution relative to the declaring script module.

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
