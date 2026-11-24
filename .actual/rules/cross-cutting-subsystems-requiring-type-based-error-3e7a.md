---
version: 2
name: cross-cutting-subsystems-requiring-type-based-error-3e7a
description: "Adoption of Standard Library errors Package for Error Handling and Inspection: Subsystems Requiring Type Based Error Discrimination"
metadata:
  actual:
    adr_id: 3e7ac3f0-a4dc-452c-a5c5-c2a3c9f51843
    status: accepted
    candidate_group_id: 2574cfc5-b33c-4e63-909a-4027ffd4b84b
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/**
      - cmd/airflow-go-pack/**
      - example/bundle/concurrentxcom/**
      - internal/bundlefooter/**
      - pkg/bundles/shared/**
      - pkg/edgeapi/**
      - pkg/execution/**
      - pkg/logging/server/**
      - pkg/worker/**
      - sdk/**
---

# Adoption of Standard Library errors Package for Error Handling and Inspection: Subsystems Requiring Type Based Error Discrimination

Scope: applies to `go-sdk/bundle/bundlev1/**`, `go-sdk/cmd/airflow-go-pack/**`, `go-sdk/example/bundle/concurrentxcom/**`, `go-sdk/internal/bundlefooter/**`, `go-sdk/pkg/bundles/shared/**`, `go-sdk/pkg/edgeapi/**`, `go-sdk/pkg/execution/**`, `go-sdk/pkg/logging/server/**`, `go-sdk/pkg/worker/**`, `go-sdk/sdk/**`.

These rules are ALWAYS ACTIVE for:
- All internal and public software development kit packages that instantiate, propagate, or inspect error conditions.
- Inter-process communication adapters, worker execution runtimes, and bundle discovery modules handling fallible operations.

### Rules

- **R-LM-001** MUST: Subsystems requiring type-based error discrimination MUST use errors.As rather than direct type assertions to inspect wrapped error hierarchies.

### Verify

- Discover the repository static analysis configuration and run linters to detect improper error formatting and ignored error returns.
- Discover the project test suite runner from the build configuration and execute unit and integration tests verifying error wrapping and unwrapping behavior.

**Accept when:**

- All unit and integration test suites pass with assertions verifying error identities and causal chains.
- Static analysis linters pass without flagging any uninspected errors or improper error-wrapping directives.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline running test suites and static analysis linters on all pull requests.
- Verified by: Peer code review verifying compliance with error creation, wrapping, and inspection rules.
- Violation handling: Pull requests containing unwrapped error chains, direct type assertions on errors, or external error library dependencies will be blocked until remediated.
- Exception process: Exceptions for specialized transport serialization layers must be reviewed and approved by the core architecture review team and documented in the module design notes.
</enforcement>
