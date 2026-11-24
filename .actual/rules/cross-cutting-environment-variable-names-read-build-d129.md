---
version: 2
name: cross-cutting-environment-variable-names-read-build-d129
description: "Environment Variable Resolution via Standard os Runtime Interface: Environment Variable Names Read Build Execution"
metadata:
  actual:
    adr_id: d1298d33-b17e-4c3f-bdf4-ff4869893ce0
    status: accepted
    candidate_group_id: ef443560-9d9d-4269-bf59-740c85651b0f
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - docs/**
      - hatch_build.py
---

# Environment Variable Resolution via Standard os Runtime Interface: Environment Variable Names Read Build Execution

Scope: applies to `airflow-core/docs/**`, `airflow-core/hatch_build.py`.

These rules are ALWAYS ACTIVE for:
- Build preparation routines and documentation generation modules accessing host environment variables.
- Scripts executing in containerized or continuous integration environments requiring configuration parameterization.

### Rules

- **R-CEM-001** MUST: All environment variable names read by build or execution modules MUST follow a standardized uppercase prefix convention aligned with the project domain.

### Verify

- Discover and run the project repository build verification script to confirm environment variable resolution succeeds under default conditions.
- Discover and execute the repository documentation build workflow with and without optional environment variables set.

**Accept when:**

- Build and documentation routines execute successfully with default fallback values when non-critical environment variables are unset.
- Missing mandatory environment variables produce clear, actionable error diagnostics rather than unhandled key lookup errors.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline execution across matrixed environment variable configurations.
- Verified by: Peer code review for pull requests modifying build hooks or documentation configuration routines.
- Violation handling: Build failures in automated validation pipelines when required environment variables fail to resolve.
- Violation handling: Rejection of pull requests introducing unchecked direct environment variable access without fallback defaults or error handling.
- Exception process: Submit an architecture review request justifying the requirement for unvalidated or ad-hoc environment access during emergency patches or legacy maintenance.
</enforcement>
