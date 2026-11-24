---
version: 2
name: cross-cutting-execution-components-configuring-runtime-560d
description: "Direct Runtime Configuration via os.environ: Execution Components Configuring Runtime Behavior Through"
metadata:
  actual:
    adr_id: 560d138d-e15b-44e8-a9d4-ca88d07273a7
    status: accepted
    candidate_group_id: 1cff565b-8a7d-4b76-96f4-c5a74327b13f
    evidence:
      source: deep
  scope:
    root: performance
    paths:
      - src/performance_dags/performance_dag/**
---

# Direct Runtime Configuration via os.environ: Execution Components Configuring Runtime Behavior Through

Scope: applies to `performance/src/performance_dags/performance_dag/**`.

These rules are ALWAYS ACTIVE for:
- Execution scripts and runtime modules that configure workload parameters through environment variables.

### Rules

- **R-CEM-001** MUST: Execution components configuring runtime behavior through environment variables MUST use os.environ dictionary indexing for mandatory parameters to fail fast when prerequisites are missing.

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
