---
version: 2
name: 802d2326-1bc6-4647-be3c-031b6ebfd12a-bundlev1-module-remote-bundle-execution-hosts-implement-bundlegrpcplugin
description: "bundlev1 Module: Remote Bundle Execution Hosts Implement Bundlegrpcplugin"
metadata:
  actual:
    adr_id: 802d2326-1bc6-4647-be3c-031b6ebfd12a
    status: accepted
    candidate_group_id: 3ee10499-1a34-4896-955a-78c69628a311
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/**
      - example/bundle/concurrentxcom/**
---

# bundlev1 Module: Remote Bundle Execution Hosts Implement Bundlegrpcplugin

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `bundle/bundlev1/**`
- `example/bundle/concurrentxcom/**`

## Context

- Workload bundles require a decoupled mechanism to register tasks and DAGs dynamically within the Go SDK.
- Execution servers require a uniform protocol contract to query metadata and execute workloads across service boundaries.
- Concurrent task execution and bundle caching necessitate thread-safe state synchronization across concurrent callers.

## Problem Statement

Decoupled task execution engines require a standardized, thread-safe module to register DAG workloads, query bundle metadata, and execute tasks across plugin boundaries without exposing server internals or introducing concurrency races.

## Decision

- **R-LM-001** MUST: Remote bundle execution hosts MUST implement the BundleGRPCPlugin interface, handling workload requests in Execute and publishing bundle details through GetMetadata.

## Policy Block

In scope:

- All Go SDK bundle registry implementations and task definitions
- All gRPC plugin implementations serving bundle metadata and workload execution

Out of scope:

- Standalone CLI utilities that do not execute or serve bundle tasks
- External client callers communicating solely over published transport protocols

## Rationale

- The bundlev1 module provides a uniform contract for DAG and task registration, isolating execution orchestration from core server internals.
- Standardizing on the BundleGRPCPlugin contract allows host runtimes to interact with dynamic bundles over structured RPC boundaries.
- Explicit registry locking through RWMutex ensures deterministic cache initialization under concurrent workload queries.

## Consequences

Positive:

- Decouples workload task definitions from execution server runtime internals.
- Enforces thread-safe bundle registration and lookup across concurrent workflows.
- Standardizes remote execution through well-defined plugin interfaces.

Negative:

- Registry access requires careful synchronization to prevent lock contention under concurrent load.
- Module developers must adhere strictly to the plugin lifecycle contracts for metadata retrieval and workload dispatch.

## Alternatives

- Direct ad-hoc function registration without an intermediate bundle registry (rejected)
  Rejected because: Lacks thread-safe lookup capabilities and prevents modular discovery across dynamic runtime plugins.
  When valid: Valid only in single-threaded scripts with hardcoded task execution paths.
- Standalone HTTP REST endpoints for workload registration and dispatch (rejected)
  Rejected because: Introduces additional serialization overhead and protocol impedance compared to gRPC plugin contracts.
  When valid: Valid when heterogeneous external services require public HTTP access without gRPC tooling.

## Risks

- Contention on bundle cache locks during high-frequency concurrent workload execution.
  Mitigation: Employ read-write lock semantics to allow concurrent read access while restricting write exclusivity to bundle initialization.
  Owner: engineering team
- Unimplemented workload types causing runtime failures during plugin dispatch.
  Mitigation: Validate incoming workload requests against registered task identifiers prior to dispatch and return explicit status codes for unimplemented workloads.
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
- Initialize registry bundles lazily and store bundle references under shared read-write mutex guards to support reentrant queries.
- Ensure workload execution methods validate task payload presence before dispatching execution to registered task handlers.

## Continuation Context

Verify commands:

- Discover the project test runner configuration from the repository manifest and execute all unit tests covering the bundle registry and server modules.
- Discover the project static analysis and linting configuration and run checks against all bundle registration and execution interface implementations.
- Discover the project concurrency and race-detector test command to verify thread-safe execution across bundle cache locking mechanisms.

Accept when:

- All unit tests covering bundle creation, task registration, and metadata reporting pass without failures.
- Static analysis validates that all bundle server implementations conform to the plugin interface contracts without unhandled errors.
- Concurrent execution tests pass with zero detected race conditions across registry cache locks.

## Enforcement

- Verified by: Automated test suites running race detection during continuous integration.
- Verified by: Static analysis and interface compliance linting in pull request pipelines.
- Verified by: Mandatory code review by core module maintainers.
- Violation handling: Pull requests introducing unmediated bundle access or missing synchronization locks will fail automated checks and be blocked from merge.
- Exception process: Exceptions require architecture review and documented approval by the SDK maintainers prior to merging deviations from bundle registry interfaces.
