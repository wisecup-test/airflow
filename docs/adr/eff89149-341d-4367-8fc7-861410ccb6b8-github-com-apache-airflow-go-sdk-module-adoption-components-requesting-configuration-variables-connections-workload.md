---
version: 2
name: eff89149-341d-4367-8fc7-861410ccb6b8-github-com-apache-airflow-go-sdk-module-adoption-components-requesting-configuration-variables-connections-workload
description: "github.com/apache/airflow/go-sdk Module Adoption: Components Requesting Configuration Variables Connections Workload"
metadata:
  actual:
    adr_id: eff89149-341d-4367-8fc7-861410ccb6b8
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

# github.com/apache/airflow/go-sdk Module Adoption: Components Requesting Configuration Variables Connections Workload

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `bundle/bundlev1/bundlev1server/**`
- `cmd/**`
- `edge/**`
- `pkg/**`
- `sdk/**`

## Context

- Distributed execution runtimes require consistent mechanisms for task execution, worker state coordination, and metadata retrieval across distributed nodes.
- Go-based execution environments need strongly typed interfaces to communicate with the coordinator API, discover DAG bundles, and evaluate environment configurations.
- Decoupling the worker runtime and bundle serving infrastructure into a dedicated internal library module establishes clear contract boundaries and prevents logic duplication across entrypoints.
- Centralizing client contracts, variable resolution, and connection decoding within a shared library module ensures uniform variable deserialization and credential extraction across edge workers and bundle servers.

## Problem Statement

Go-based worker nodes and bundle plugins require a standardized, decoupled integration layer to interface with the orchestration plane. Without a unified internal library module, separate worker implementations and bundle servers duplicate communication protocols, secret masking logic, environment resolution rules, and variable decoding routines, causing behavioral divergence and maintenance overhead across execution components.

## Decision

- **R-LM-001** MUST: Components requesting configuration variables, connections, or workload metadata MUST invoke the standardized client contracts provided by the library rather than issuing raw network requests.

## Policy Block

In scope:

- Components implementing Go-based task workers, edge workers, and command entrypoints.
- Modules providing DAG bundle discovery, gRPC bundle serving, and workload execution.
- Internal packages establishing client connections to the execution coordinator API.

Out of scope:

- External services and orchestrator core components implemented in other runtime ecosystems.
- Third-party plugins operating outside the Go execution subsystem boundaries.

## Rationale

- Establishing the github.com/apache/airflow/go-sdk module ensures consistent behavioral parity across edge workers, bundle plugins, and execution clients.
- Centralizing client contracts prevents configuration drift between direct API communication channels and coordinator message protocols.
- Encapsulating connection decoding, variable resolution, and authentication headers within shared library contracts minimizes attack surfaces and standardizes credential handling.
- Modular packaging allows independent verification and lifecycle maintenance for Go worker components without creating circular dependencies with the core platform.

## Consequences

Positive:

- Standardized client contracts eliminate drift in variable decoding, connection parsing, and task state handling.
- Strict package separation enables independent testing and modular compilation of lightweight worker binaries.
- Shared interfaces simplify bundle discovery and gRPC server orchestration across heterogeneous deployment targets.

Negative:

- Changes to core execution contracts require synchronized updates and releases across all dependent worker packages.
- Additional abstraction layers create development overhead for simple or experimental worker routines.
- Cross-language protocol alignment between the Go SDK and the primary orchestration platform requires ongoing maintenance.

## Alternatives

- Ad hoc in-tree HTTP client calls and bespoke worker routines per binary (rejected)
  Rejected because: Dispersing client logic across multiple entrypoints causes inconsistent environment lookup precedence, unhandled decoding corner cases, and protocol drift.
  When valid: Valid only during early single-file exploratory prototypes where production coordination guarantees are unnecessary.
- Externalized third-party microservice broker for task dispatching (rejected)
  Rejected because: Introduces an additional network hop and operational dependency when native Go library interfaces satisfy all execution requirements directly.
  When valid: Valid when the orchestration ecosystem enforces an enterprise-wide polyglot service mesh for all inter-process communication.

## Risks

- Inconsistent secret masking across client implementations could expose credentials in task log output.
  Mitigation: Enforce automated masking interceptors at the shared client boundary before log propagation.
  Owner: engineering team
- Dependency version drift between the repository manifest and resolved dependencies could induce runtime panic or protocol mismatches.
  Mitigation: Mandate strict lock-file resolution verification in the continuous integration pipeline prior to build execution.
  Owner: engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Ensure all new client communication methods adhere to context-aware interfaces to support graceful worker shutdown and cancellation signals.
- Consolidate shared environment variable prefixes and fallback resolution logic inside common package definitions to maintain identical precedence across all worker clients.

## Continuation Context

Verify commands:

- Inspect the project dependency manifest and lock artifact to confirm dependency tree integrity.
- Execute the project static analyzer and test suite covering the internal library module packages.
- Execute the package verification and linter suites defined in the repository continuous integration configuration.

Accept when:

- All unit and integration tests across the internal library packages pass without failures or race conditions.
- All public client contracts satisfy static type checks and linting rules enforced by the continuous integration pipeline.
- No direct unmasked credential reads or duplicate variable resolution implementations exist outside the library module.

## Enforcement

- Verified by: Continuous integration static analysis checks and automated test runs on all pull requests.
- Verified by: Peer code review verifying compliance with library module boundaries and contract usage.
- Violation handling: Pull requests introducing duplicate client communication or bypassed library interfaces are blocked from merging.
- Violation handling: Violations identified during reviews require refactoring into the standardized library package contracts.
- Exception process: Exceptions require documented architectural approval and an explicit migration plan to reintegrate with the library module.
