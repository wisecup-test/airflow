---
version: 2
name: 7cb15777-4b22-47a2-8acf-008a69f35193-adoption-of-hashicorp-go-plugin-for-external-dag-bundle-protocol-execution-external-dag-bundle-extensions-implemented-executed
description: "Adoption of HashiCorp go-plugin for External DAG Bundle Protocol Execution: External Dag Bundle Extensions Implemented Executed"
metadata:
  actual:
    adr_id: 7cb15777-4b22-47a2-8acf-008a69f35193
    status: accepted
    candidate_group_id: 42449946-8874-4de8-a745-14dbba120442
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/**
      - dags/**
      - example/bundle/**
      - internal/protov1/**
      - pkg/api/**
      - pkg/bundles/shared/**
      - pkg/edgeapi/**
      - pkg/execution/**
      - pkg/logging/server/**
      - pkg/worker/**
---

# Adoption of HashiCorp go-plugin for External DAG Bundle Protocol Execution: External Dag Bundle Extensions Implemented Executed

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `bundle/bundlev1/**`
- `dags/**`
- `example/bundle/**`
- `internal/protov1/**`
- `pkg/api/**`
- `pkg/bundles/shared/**`
- `pkg/edgeapi/**`
- `pkg/execution/**`
- `pkg/logging/server/**`
- `pkg/worker/**`

## Context

- Workflow orchestration engines must support extensible user workloads and custom bundle packages without compromising host stability.
- Executing external or third-party bundle code directly inside the host process risks crash propagation, memory corruption, and runtime dependency collisions.
- Dynamic linking across diverse environments introduces binary compatibility issues and platform-specific packaging constraints.
- A structured out-of-process protocol is required to negotiate capability discovery, metadata queries, and task dispatching over isolated process boundaries.

## Problem Statement

Integrating user-defined DAG bundles directly into worker host runtimes introduces severe stability and isolation risks. Process crashes or dependency conflicts in external bundle code can compromise the primary execution daemon. The platform requires a resilient, protocol-driven integration architecture that isolates external bundle code into separate OS processes while providing efficient, strongly-typed remote procedure call mechanisms.

## Decision

- **R-PEA-001** MUST: External DAG bundle extensions MUST be implemented and executed as separate OS processes communicating via HashiCorp go-plugin over gRPC rather than through in-process dynamic library loading or shared memory.

## Policy Block

In scope:

- All external DAG bundle authoring and runtime integration protocols requiring out-of-process execution boundaries.
- Workload and metadata extraction tasks executed through external bundle binaries.

Out of scope:

- Internal coordinator-to-worker streaming communications operating over dedicated supervisor IPC channels.
- Standard HTTP REST API clients communicating with centralized orchestration endpoints.

## Rationale

- Adopting HashiCorp go-plugin enforces process-level isolation between the orchestration engine and external bundle code, ensuring fatal errors do not destabilize the host daemon.
- Standardizing on gRPC as the underlying transport provides strongly-typed service contracts, multi-platform compatibility, and structured error handling across process boundaries.
- The plugin architecture decouples the lifecycle of bundle binaries from worker runtimes, allowing independent updates and language-agnostic extension points.

## Consequences

Positive:

- Protects the host daemon from crashes, memory corruption, and unhandled panics originating in external bundle logic.
- Enforces explicit, strongly-typed interface boundaries for metadata retrieval and workload execution.
- Simplifies dependency management by completely isolating bundle-specific libraries from host engine dependencies.

Negative:

- Introduces inter-process communication overhead for remote procedure calls and payload serialization.
- Requires managing external process lifecycles, monitoring subprocess health, and handling process termination edge cases.
- Increases architectural complexity compared to direct in-process function invocation.

## Alternatives

- In-process dynamic library loading via shared object symbols (rejected)
  Rejected because: Shared library loading tightly couples dependencies to the host binary runtime, causes catastrophic host termination on unhandled panics, and suffers from platform-specific binary compatibility issues.
  When valid: Only viable in tightly controlled monolithic environments where zero serialization overhead is strictly required and all code is compiled from a single synchronized tree.
- Standard HTTP REST microservice endpoints over network sockets (rejected)
  Rejected because: Network microservices introduce network port management overhead, lack automated process lifetime management, and incur higher transport latency than local IPC plugin brokers.
  When valid: Appropriate when bundle executors run on physically remote infrastructure or distributed compute clusters.

## Risks

- Orphaned subprocesses consuming system resources if the host terminates unexpectedly without cleaning up child processes.
  Mitigation: Utilize built-in plugin health checks, broker death listeners, and operating system process group tracking to terminate child processes when the host connection drops.
  Owner: engineering team
- Serialization latency and memory consumption when passing large task workload payloads across the gRPC transport boundary.
  Mitigation: Keep inter-process message schemas focused on metadata and control signals, passing heavy state through shared storage references rather than large in-memory payloads.
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
- Configure plugin handshake parameters consistently between client dispatchers and server endpoints to ensure process negotiation succeeds.
- Structure remote procedure call method handlers to catch workload panics and translate them into structured error responses across the plugin boundary.

## Continuation Context

Verify commands:

- Inspect the project repository manifest and build configuration to identify the plugin integration test runner, then execute the suite to verify subprocess launch, handshake negotiation, and gRPC communication.
- Discover and execute the protocol compliance validation scripts to ensure all client and server implementations satisfy the bundle service specification.
- Run the repository static analysis and linting targets to confirm strict conformance with plugin interface contracts and error handling requirements.

Accept when:

- All external bundle plugin client and server test suites pass with successful subprocess handshake and execution.
- Static analysis confirms full compliance with the plugin interface specification without contract deviations.
- Context cancellation signals properly propagate to terminate running child workloads without process leaks.

## Enforcement

- Verified by: Automated continuous integration test suites validating plugin lifecycle and contract execution.
- Verified by: Repository static analysis and architecture linting rules blocking direct in-process bundle loading.
- Verified by: Peer code review verifying adherence to plugin protocol boundaries and error translation standards.
- Violation handling: Pull requests attempting in-process execution of external bundle workloads are automatically blocked in continuous integration.
- Violation handling: Submissions with unhandled plugin boundary errors or missing process cleanup logic are rejected during code review.
- Exception process: Exceptions requiring direct in-process execution must be submitted to the architecture review board with performance profiling data and isolation impact assessments.
- Exception process: Approved exceptions must be documented in an architectural exception record with explicit review and deprecation dates.
