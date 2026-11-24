---
version: 2
name: a986d77a-af05-4c6a-b15f-17f36eeaf67a-hashicorp-go-plugin-dag-bundle-contract-architecture-plugin-client-implementations-implement-grpcclient-interface
description: "HashiCorp go-plugin DAG Bundle Contract Architecture: Plugin Client Implementations Implement Grpcclient Interface"
metadata:
  actual:
    adr_id: a986d77a-af05-4c6a-b15f-17f36eeaf67a
    status: accepted
    candidate_group_id: a5a5e46e-a87d-41de-a477-56792ab9d164
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/**
      - dags/**
      - edge/**
      - example/bundle/**
      - internal/**
      - pkg/**
      - sdk/**
---

# HashiCorp go-plugin DAG Bundle Contract Architecture: Plugin Client Implementations Implement Grpcclient Interface

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `bundle/bundlev1/**`
- `dags/**`
- `edge/**`
- `example/bundle/**`
- `internal/**`
- `pkg/**`
- `sdk/**`

## Context

- Worker processes and task execution runtimes require strict process isolation to prevent user-defined DAG execution from destabilizing core orchestration services.
- The Go SDK provides an extensible plugin interface where DAG bundles expose metadata discovery and task workload execution over inter-process communication protocols.
- The codebase standardizes on HashiCorp go-plugin backed by gRPC interfaces to define typed communication contracts between the worker host and DAG bundle servers.

## Problem Statement

Executing user-defined DAG workloads and resolving task metadata within the same memory space as the orchestration worker introduces critical reliability, security, and lifecycle coupling risks. The system requires an extensible, strongly typed inter-process communication mechanism that isolates bundle execution into independent processes while preserving clean request-response semantics for metadata inspection and task execution.

## Decision

- **R-PEA-001** MUST: Plugin client implementations MUST implement the GRPCClient interface and delegate GetMetadata and ExecuteTaskWorkload operations to the underlying gRPC client stubs with context propagation.

## Policy Block

In scope:

- DAG bundle implementations providing task and DAG definitions to the Go SDK worker runtime.
- Out-of-process execution boundaries between the worker host and bundle plugins.

Out of scope:

- In-process internal utility helpers and logging bridges.
- Direct HTTP REST API client communication with the external orchestration API server.

## Rationale

- Evidence across multiple bundle packages establishes that isolating execution behind HashiCorp go-plugin gRPC boundaries guarantees process resilience against memory leaks, segmentation faults, or unhandled panics in bundle code.
- Standardizing on defined GRPCClient and GRPCServer contracts allows independent deployment and versioning of task execution plugins without altering worker orchestration logic.
- Typed contract definitions for metadata retrieval and workload execution ensure compile-time and runtime validation across the process boundary.

## Consequences

Positive:

- Strong fault isolation ensures that crashes or panics in user DAG code do not terminate the host worker process.
- Clear contract boundaries simplify independent testing and mocking of client and server bundle components.
- Standardized protocol-driven execution permits distinct dependency trees and runtime configurations for individual bundles.

Negative:

- Inter-process gRPC communication adds latency and CPU serialization overhead compared to in-process execution.
- Managing external plugin processes introduces additional lifecycle management, process monitoring, and IPC handshake complexity.
- Changes to bundle schema contracts require synchronized updates across both client wrappers and server endpoints.

## Alternatives

- In-process dynamic shared library loading (rejected)
  Rejected because: Shared libraries share the process address space, causing fatal panics or crashes in user code to terminate the host worker daemon.
  When valid: Acceptable only in monolithic, single-tenant runtimes where inter-process serialization overhead cannot be tolerated.
- Standard HTTP REST service plugins (rejected)
  Rejected because: Lacks bidirectional streaming capabilities, robust subprocess lifecycle management, and typed RPC contract generation provided by gRPC and go-plugin.
  When valid: Valid when plugins are deployed as independent long-running network microservices rather than managed local subprocesses.

## Risks

- Subprocess leaks or orphaned plugin processes when the host worker encounters abnormal termination.
  Mitigation: Implement robust signal handling, graceful drain sequences, and health check heartbeats to terminate child plugin processes.
  Owner: engineering team
- Performance degradation due to message serialization across the IPC boundary for high-throughput task workloads.
  Mitigation: Optimize protobuf payload representations and reuse persistent gRPC client connections across task invocations.
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
- Implementers must ensure all plugin client calls pass context instances with appropriate timeouts to prevent indefinite blocking during RPC communication.
- Bundle server configurations should integrate structured logging adapters to pipe child process logs back to the supervisor logging handler.

## Continuation Context

Verify commands:

- Discover and run the test suite for plugin client and server contract implementations from the project repository configuration.
- Discover and execute the repository static analysis and linting checks to verify interface compliance.

Accept when:

- All plugin client and server test suites pass without contract mismatches or runtime panics.
- Subprocess lifecycle tests verify that plugins terminate cleanly upon host shutdown or context cancellation.

## Enforcement

- Verified by: Automated contract testing and integration test suites executed during continuous integration.
- Verified by: Architecture and code reviews for modifications to bundle interfaces or gRPC definitions.
- Violation handling: Build failures triggered when contract signatures or plugin registrations deviate from the defined interface.
- Violation handling: Rejection of pull requests that bypass the out-of-process plugin boundary.
- Exception process: Architectural review submission detailing performance or operational justification for alternative execution models, requiring approval from the core SDK maintainers.
