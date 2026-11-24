---
version: 2
name: cross-cutting-components-reading-optional-runtime-conf-f461
description: "Direct Runtime Configuration via os.environ: Components Reading Optional Runtime Configuration Parameters"
metadata:
  actual:
    adr_id: f461154b-8bf1-4aa5-b929-d54e1b799f78
    status: accepted
    candidate_group_id: 1cff565b-8a7d-4b76-96f4-c5a74327b13f
    evidence:
      source: deep
  scope:
    root: performance
    paths:
      - src/performance_dags/performance_dag/**
---

# Direct Runtime Configuration via os.environ: Components Reading Optional Runtime Configuration Parameters

Scope: applies to `performance/src/performance_dags/performance_dag/**`.

These rules are ALWAYS ACTIVE for:
- Execution scripts and runtime modules that configure workload parameters through environment variables.

### Rules

- **R-CEM-001** MUST: Components reading optional runtime configuration parameters MUST use os.environ.get with explicit fallback default values.

### Verify

- Discover and run the project static analysis and linting suite to verify compliant usage of os.environ and os.environ.get.
- Discover and run the automated test suite with missing and populated environment variables to verify fail-fast and fallback behaviors.

**Accept when:**

- Static analysis validates that all optional environment variable reads specify a fallback default.
- Test suites pass with expected errors raised when mandatory environment variables are omitted.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated linting and static analysis checks during integration pipelines.
- Verified by: Peer code review for execution scripts accessing runtime configuration.
- Violation handling: Static analysis failures block merging of code accessing environment variables without required fallbacks or proper indexing.
- Exception process: Exceptions must be documented with an architectural justification and approved by the engineering leads.
</enforcement>
