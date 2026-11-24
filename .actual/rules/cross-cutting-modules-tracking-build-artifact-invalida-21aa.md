---
version: 2
name: cross-cutting-modules-tracking-build-artifact-invalida-21aa
description: "airflow_breeze.utils.cache Internal Module Adoption: Modules Tracking Build Artifact Invalidation Coordinate"
metadata:
  actual:
    adr_id: 21aa3d13-a172-45fa-be69-7be8a4830215
    status: accepted
    candidate_group_id: cfb6ad1d-6ce2-487e-aac2-84a1dc3ecabd
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/**
      - breeze/src/airflow_breeze/**
      - registry/**
---

# airflow_breeze.utils.cache Internal Module Adoption: Modules Tracking Build Artifact Invalidation Coordinate

Scope: applies to `dev/airflow_perf/**`, `dev/breeze/src/airflow_breeze/**`, `dev/registry/**`.

These rules are ALWAYS ACTIVE for:
- Internal developer environment utilities, CLI subcommands, and build orchestration routines managing execution state or parameter persistence.

### Rules

- **R-LM-001** SHOULD: Modules tracking build artifact invalidation SHOULD coordinate with cache check contracts to bypass unnecessary build tasks when cache states indicate fresh artifacts.

### Verify

- Discover the project test runner configuration from the repository manifest and execute test suites targeting developer utility modules.
- Discover the repository static analysis and linting configurations and execute style checks against internal utility packages.

**Accept when:**

- All unit tests exercising cache reading, writing, and validation contracts pass successfully without file system access errors.
- Static type checking and architectural boundary checks confirm that no modules bypass the public cache contract.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites exercising caching utility contracts.
- Verified by: Peer code review verifying adherence to the centralized cache module interface.
- Violation handling: Pull requests bypassing the cache module in favor of direct file system mutation will be blocked.
- Violation handling: Static analysis violations or test failures will fail automated verification pipelines.
- Exception process: Exceptions require documented justification submitted to and approved by the tooling maintainers.
</enforcement>
