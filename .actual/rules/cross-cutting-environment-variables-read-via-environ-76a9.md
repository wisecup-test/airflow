---
version: 2
name: cross-cutting-environment-variables-read-via-environ-76a9
description: "Direct Runtime Configuration via os.environ: Environment Variables Read Via Environ Share"
metadata:
  actual:
    adr_id: 76a95634-23f3-4bac-aa9d-97abb8180de3
    status: accepted
    candidate_group_id: 1cff565b-8a7d-4b76-96f4-c5a74327b13f
    evidence:
      source: deep
  scope:
    root: performance
    paths:
      - src/performance_dags/performance_dag/**
---

# Direct Runtime Configuration via os.environ: Environment Variables Read Via Environ Share

Scope: applies to `performance/src/performance_dags/performance_dag/**`.

These rules are ALWAYS ACTIVE for:
- Execution scripts and runtime modules that configure workload parameters through environment variables.

### Rules

- **R-CEM-001** SHOULD: Environment variables read via os.environ SHOULD share a domain-specific prefix to prevent collision across execution boundaries.

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
