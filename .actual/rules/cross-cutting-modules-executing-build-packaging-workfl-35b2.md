---
version: 2
name: cross-cutting-modules-executing-build-packaging-workfl-35b2
description: "Standard logging Module Logger Hierarchy for Build Infrastructure: Modules Executing Build Packaging Workflows Resolve"
metadata:
  actual:
    adr_id: 35b22f5e-737b-454a-ab08-31afc85e7de2
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

# Standard logging Module Logger Hierarchy for Build Infrastructure: Modules Executing Build Packaging Workflows Resolve

Scope: applies to `airflow-core/docs/**`, `airflow-core/hatch_build.py`.

These rules are ALWAYS ACTIVE for:
- Build hook scripts, packaging automation routines, and documentation configuration modules within the repository.
- Operational tooling scripts that execute during build, compilation, or asset preparation phases.

### Rules

- **R-L-001** SHOULD: Modules executing build or packaging workflows SHOULD resolve initial log levels dynamically from designated environment variables via getenv using getLevelName, falling back to a standard informational threshold when unset.

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
