---
version: 2
name: be182142-7c07-4f40-9a50-e7a8c754066a-adopt-github-com-hashicorp-go-plugin-for-out-of-process-grpc-plugin-architecture-before-writing-code-that-uses-updates
description: "Adopt github.com/hashicorp/go-plugin for Out-of-Process gRPC Plugin Architecture: Before Writing Code That Uses Updates"
metadata:
  actual:
    adr_id: be182142-7c07-4f40-9a50-e7a8c754066a
    status: accepted
    candidate_group_id: f4170281-3dae-4443-8ab0-e6ccca575b75
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/bundlev1client/**
      - pkg/bundles/shared/**
---

# Adopt github.com/hashicorp/go-plugin for Out-of-Process gRPC Plugin Architecture: Before Writing Code That Uses Updates

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `bundle/bundlev1/bundlev1client/**`
- `pkg/bundles/shared/**`

## Context

- Bundle execution and metadata retrieval require extensible integration points that support modular, isolated execution.
- Running third-party or user-supplied bundle workloads in the same address space creates operational risks of memory corruption and unhandled crashes affecting the host.
- Standardizing plugin communication over gRPC via github.com/hashicorp/go-plugin provides established process supervision and structured contract negotiation.

## Problem Statement

Integrating dynamic bundle workloads directly into the core host process exposes the system to crashes, memory leaks, and version coupling. The host requires a decoupled, fault-isolated plugin architecture that standardizes lifecycle supervision, handshake negotiation, and structured remote procedure call interfaces without sharing address spaces.

## Decision

- **R-LM-001** MUST: Before writing code that uses or updates github.com/hashicorp/go-plugin or associated transport libraries, the consumer MUST inspect the repository dependency manifest and authoritative lock artifact to verify the exact resolved version against public API documentation.

## Policy Block

In scope:

- All bundle client and server implementations interacting across process boundaries.
- Plugin extension points providing metadata retrieval or workload execution capabilities.

Out of scope:

- Internal utility functions operating strictly within a single process.
- Direct intra-process package dependencies that do not cross plugin boundaries.

## Rationale

- Using github.com/hashicorp/go-plugin provides out-of-process fault isolation, ensuring plugin failures do not terminate the host process.
- Leveraging gRPC transport over standard plugin brokers ensures strongly typed communication contracts with multiplexed streaming support.
- Adopting an established plugin framework avoids the maintenance burden of building custom inter-process communication protocols.

## Consequences

Positive:

- Enforces strong memory and crash isolation between the host application and external bundle plugins.
- Decouples host and plugin development lifecycles while maintaining strict schema-driven RPC contracts.
- Enables independent deployment and execution scaling of plugin workloads.

Negative:

- Introduces serialization and IPC latency overhead compared to direct in-process function invocation.
- Requires managing process lifecycle, handshake protocol negotiation, and connection failure recovery across boundaries.
- Increases debugging complexity when diagnosing errors occurring across inter-process gRPC boundaries.

## Alternatives

- In-process dynamic library loading (shared object plugins) (rejected)
  Rejected because: In-process dynamic libraries lack memory and crash fault isolation, constrain host and plugin runtimes to identical build configurations, and present memory safety and operational risks.
  When valid: Valid only when latency constraints strictly preclude inter-process communication serialization overhead.
- Custom inter-process communication protocol over standard input/output (rejected)
  Rejected because: Custom IPC protocols incur ongoing maintenance burden for message framing, serialization, streaming, and multiplexing that standardized gRPC and plugin brokers already resolve.
  When valid: Valid only when external binary size constraints prohibit embedding RPC frameworks.

## Risks

- Incompatible protocol revisions between host and plugin could lead to failed handshakes or runtime connection errors.
  Mitigation: Enforce versioned handshake configurations and implement explicit protocol negotiation checks.
  Owner: Engineering Team
- Unresponsive plugin processes may block host execution or consume excess resources.
  Mitigation: Implement strict connection timeouts, health checks, and process supervisor recovery logic.
  Owner: Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Configure handshake parameters consistently between host and plugin modules to ensure compatible negotiation.
- Wrap generated gRPC client instances within high-level SDK interface implementations to isolate callers from RPC transport details.

## Continuation Context

Verify commands:

- Discover and run the repository build script to confirm successful compilation of all plugin interfaces and client implementations.
- Execute the repository test suite through the project test runner to validate plugin handshake negotiation and client-server gRPC communication contracts.

Accept when:

- All plugin client and server interfaces compile without error against the resolved plugin library.
- Handshake negotiation and gRPC bundle client methods pass all automated unit and integration tests.

## Enforcement

- Verified by: Automated repository build and test pipeline checks validating plugin handshake and client implementations.
- Verified by: Architectural and code review of all pull requests introducing new plugin contracts or modifying plugin lifecycle handling.
- Violation handling: Pull requests implementing non-standard plugin transports or direct in-process loading will be blocked in code review.
- Violation handling: Violating code must be refactored to conform to the established go-plugin gRPC interface pattern.
- Exception process: Submit an architectural review request detailing the latency or deployment constraints that prevent using out-of-process gRPC plugins.
- Exception process: Obtain formal sign-off from the technical lead prior to introducing any alternative IPC or in-process extension mechanism.
