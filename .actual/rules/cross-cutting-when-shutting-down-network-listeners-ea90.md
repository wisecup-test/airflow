---
version: 2
name: cross-cutting-when-shutting-down-network-listeners-ea90
description: "Adoption of context Package for Structured Concurrency and Lifecycle Management: When Shutting Down Network Listeners Background"
metadata:
  actual:
    adr_id: ea90cfe0-ac97-4625-b2e6-fa017f5929dd
    status: accepted
    candidate_group_id: 61c40993-d5e6-45b1-b980-4a9bffb54265
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - edge/**
      - example/bundle/**
      - pkg/execution/**
      - pkg/logging/server/**
      - pkg/worker/**
---

# Adoption of context Package for Structured Concurrency and Lifecycle Management: When Shutting Down Network Listeners Background

Scope: applies to `go-sdk/edge/**`, `go-sdk/example/bundle/**`, `go-sdk/pkg/execution/**`, `go-sdk/pkg/logging/server/**`, `go-sdk/pkg/worker/**`.

These rules are ALWAYS ACTIVE for:
- All asynchronous worker daemon routines, background polling loops, and heartbeat runners.
- Network communication coordinators, frame dispatchers, and embedded HTTP log servers.
- Concurrent sub-task execution routines requiring parallel fan-out and result aggregation.

### Rules

- **R-PS-001** MUST: When shutting down network listeners or background servers upon Context cancellation, services MUST instantiate a bounded timeout Context using WithTimeout to allow ongoing requests to drain before forcing termination.

### Verify

- Discover the project test runner configuration from the repository manifest and execute all unit and concurrency integration tests.
- Discover and run the project static analysis and race detector tools specified in the build scripts to verify absence of data races in asynchronous dispatchers.

**Accept when:**

- All unit and integration test suites pass with zero concurrency race conditions reported by static analysis and race detection tools.
- Worker routines, communication dispatchers, and server instances terminate cleanly within timeout boundaries upon Context cancellation without leaking goroutines.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration pipelines executing concurrency test suites and race detection analysis.
- Verified by: Mandatory peer code review verifying Context propagation and proper channel select handling on all asynchronous routines.
- Violation handling: Continuous integration failure blocking merge for any code introducing goroutine leaks or race conditions.
- Violation handling: Rejection of pull requests that omit Context parameters from asynchronous public interfaces or ignore cancellation signals.
- Exception process: Submit an architectural review request documenting why an asynchronous routine cannot accept Context and detailing alternate deterministic lifecycle management.
</enforcement>
