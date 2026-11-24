---
version: 2
name: cross-cutting-before-implementing-changes-involving-ve-01f0
description: "Standard logging Module Logger Hierarchy for Build Infrastructure: Before Implementing Changes Involving Versioned Dependencies"
metadata:
  actual:
    adr_id: 01f07ab5-366f-4933-ace6-54fcfdbb6567
    status: accepted
    candidate_group_id: ce46c2ca-4441-4f8e-b768-7290cfe59bf4
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - docs/**
      - hatch_build.py
---

# Standard logging Module Logger Hierarchy for Build Infrastructure: Before Implementing Changes Involving Versioned Dependencies

Scope: applies to `airflow-core/docs/**`, `airflow-core/hatch_build.py`.

These rules are ALWAYS ACTIVE for:
- Build hook scripts, packaging automation routines, and documentation configuration modules within the repository.
- Operational tooling scripts that execute during build, compilation, or asset preparation phases.

### Rules

- **R-L-001** MUST: Before implementing changes involving versioned dependencies, engineers MUST discover the dependency manifest and authoritative lock artifact in the repository to verify exact dependency resolutions.

### Verify

- Discover and run the repository static analysis and linting scripts to verify logger imports and check that raw print statements are absent from infrastructure scripts.
- Discover and run the build validation pipeline with custom log level environment variables configured to verify dynamic threshold propagation.

**Accept when:**

- Static analysis validates that all build and documentation scripts utilize getLogger rather than raw standard stream printing.
- Executing build verification scripts with elevated log level environment variables displays expected diagnostic log messages.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks in continuous integration pipelines.
- Verified by: Peer code review for changes affecting build and documentation scripts.
- Violation handling: Pull requests containing direct standard stream printing in infrastructure scripts will fail automated linting and require remediation.
- Exception process: Submit an architectural review request detailing why structured logging cannot meet specific CLI or stream piping requirements.
</enforcement>
