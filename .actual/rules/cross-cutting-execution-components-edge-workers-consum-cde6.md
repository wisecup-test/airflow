---
version: 2
name: cross-cutting-execution-components-edge-workers-consum-cde6
description: "github.com/apache/airflow/go-sdk Module Adoption: Execution Components Edge Workers Consume Github"
metadata:
  actual:
    adr_id: cde6b6ba-12db-4e37-97e5-9fc705f90569
    status: accepted
    candidate_group_id: 6b2b37a0-01df-4bcb-b12b-448c450193fa
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/bundlev1server/**
      - cmd/**
      - edge/**
      - pkg/**
      - sdk/**
---

# github.com/apache/airflow/go-sdk Module Adoption: Execution Components Edge Workers Consume Github

Scope: applies to `go-sdk/bundle/bundlev1/bundlev1server/**`, `go-sdk/cmd/**`, `go-sdk/edge/**`, `go-sdk/pkg/**`, `go-sdk/sdk/**`.

These rules are ALWAYS ACTIVE for:
- Components implementing Go-based task workers, edge workers, and command entrypoints.
- Modules providing DAG bundle discovery, gRPC bundle serving, and workload execution.
- Internal packages establishing client connections to the execution coordinator API.

### Rules

- **R-LM-001** MUST: All Go execution components and edge workers MUST consume the github.com/apache/airflow/go-sdk internal library module for orchestration communication, client construction, and workload lifecycle management.

### Verify

- Inspect the project dependency manifest and lock artifact to confirm dependency tree integrity.
- Execute the project static analyzer and test suite covering the internal library module packages.
- Execute the package verification and linter suites defined in the repository continuous integration configuration.

**Accept when:**

- All unit and integration tests across the internal library packages pass without failures or race conditions.
- All public client contracts satisfy static type checks and linting rules enforced by the continuous integration pipeline.
- No direct unmasked credential reads or duplicate variable resolution implementations exist outside the library module.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration static analysis checks and automated test runs on all pull requests.
- Verified by: Peer code review verifying compliance with library module boundaries and contract usage.
- Violation handling: Pull requests introducing duplicate client communication or bypassed library interfaces are blocked from merging.
- Violation handling: Violations identified during reviews require refactoring into the standardized library package contracts.
- Exception process: Exceptions require documented architectural approval and an explicit migration plan to reintegrate with the library module.
</enforcement>
