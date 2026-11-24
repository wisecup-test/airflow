---
version: 2
name: cross-cutting-direct-operating-system-environment-quer-428a
description: "Viper Adoption for Runtime Configuration and Environment Management: Direct Operating System Environment Queries Not"
metadata:
  actual:
    adr_id: 428abca3-35ea-4093-81b7-21f17c2f0c40
    status: accepted
    candidate_group_id: 50f3417c-6a7c-4c41-9d8f-c6bc622af394
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/bundlev1server/impl/**
      - cmd/airflow-go-edge-worker/**
      - cmd/airflow-go-pack/**
      - edge/**
      - pkg/bundles/shared/**
      - pkg/config/**
      - pkg/worker/**
---

# Viper Adoption for Runtime Configuration and Environment Management: Direct Operating System Environment Queries Not

Scope: applies to `go-sdk/bundle/bundlev1/bundlev1server/impl/**`, `go-sdk/cmd/airflow-go-edge-worker/**`, `go-sdk/cmd/airflow-go-pack/**`, `go-sdk/edge/**`, `go-sdk/pkg/bundles/shared/**`, `go-sdk/pkg/config/**`, `go-sdk/pkg/worker/**`.

These rules are ALWAYS ACTIVE for:
- All runtime packages, worker services, CLI binaries, and plugin modules requiring configurable runtime parameters.

### Rules

- **R-CEM-001** MUST_NOT: Direct operating system environment queries MUST NOT be used for general application settings, but MAY be used for process bootstrap variables and early runtime bootstrap coordination.

### Verify

- Discover and run the repository test suite to validate configuration binding and parsing behavior.
- Execute the repository code formatting and static analysis checks to ensure configuration API compliance.

**Accept when:**

- All unit and integration tests verifying configuration loading and flag bindings pass without failure.
- Static analysis validates that application configuration lookups use the designated configuration interface.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites.
- Verified by: Static analysis rules and peer code reviews during pull request inspection.
- Violation handling: Pull requests with unauthorized direct file parsing or bypassing configuration access patterns will be blocked in code review.
- Violation handling: Violating components must be refactored to consume the centralized configuration provider or accept configuration via domain structures.
- Exception process: Submit an architecture exception request detailing why the centralized configuration subsystem cannot be used during component initialization.
- Exception process: Obtain approval from the Runtime Architecture Lead prior to merging code that introduces direct environment or file parsing.
</enforcement>
