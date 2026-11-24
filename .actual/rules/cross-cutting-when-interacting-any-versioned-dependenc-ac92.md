---
version: 2
name: cross-cutting-when-interacting-any-versioned-dependenc-ac92
description: "airflow_breeze.utils.cache Internal Module Adoption: When Interacting Any Versioned Dependency That"
metadata:
  actual:
    adr_id: ac928527-c4f1-472c-bef9-3c5ce9e5a6cd
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

# airflow_breeze.utils.cache Internal Module Adoption: When Interacting Any Versioned Dependency That

Scope: applies to `dev/airflow_perf/**`, `dev/breeze/src/airflow_breeze/**`, `dev/registry/**`.

These rules are ALWAYS ACTIVE for:
- Internal developer environment utilities, CLI subcommands, and build orchestration routines managing execution state or parameter persistence.

### Rules

- **R-LM-001** MUST: When interacting with any versioned dependency that interfaces with the caching module, developers MUST inspect the repository lock artifact and verify the exact resolved dependency version before introduction.

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
