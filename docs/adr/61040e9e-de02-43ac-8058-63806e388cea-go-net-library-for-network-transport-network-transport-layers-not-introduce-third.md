---
version: 2
name: 61040e9e-de02-43ac-8058-63806e388cea-go-net-library-for-network-transport-network-transport-layers-not-introduce-third
description: "Go net Library for Network Transport: Network Transport Layers Not Introduce Third"
metadata:
  actual:
    adr_id: 61040e9e-de02-43ac-8058-63806e388cea
    status: accepted
    candidate_group_id: 21ebb723-83a4-4477-84af-f6b025953fd8
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/edgeapi/**
      - pkg/execution/**
---

# Go net Library for Network Transport: Network Transport Layers Not Introduce Third

Date: 2025-05-15
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `pkg/edgeapi/**`
- `pkg/execution/**`

## Context

- The software development kit requires network communication capabilities for client requests and runtime server connectivity.
- Core transport operations currently utilize the language runtime standard networking package rather than introducing external third-party networking dependencies.
- Standard library networking primitives provide socket dialing, listener binding, and protocol handling without external supply-chain overhead.

## Problem Statement

Network communication components in the software development kit must establish reliable client-server and inter-process transport connections without introducing unvetted third-party runtime dependencies or dependency version conflicts for downstream consumers.

## Decision

- **R-LM-001** MUST_NOT: Network transport layers MUST NOT introduce third-party networking or HTTP client wrappers where standard library net and HTTP capabilities satisfy protocol requirements.

## Policy Block

In scope:

- Network transport, socket communication, and client-server request handling across software development kit packages

Out of scope:

- In-memory message passing and non-networked data transformation utilities

## Rationale

- Relying on the standard library net module eliminates third-party supply chain risks and dependency conflicts for consumers of the software development kit.
- Standard networking primitives provide direct control over connection lifecycles, connection dialing, and protocol framing.
- Minimizing external dependencies keeps the SDK lightweight and ensures long-term API stability across language runtime releases.

## Consequences

Positive:

- Zero external dependency footprint for foundational networking and client transport.
- Consistent transport semantics across client API interactions and background runtime server routines.
- Reduced exposure to third-party dependency vulnerabilities and breaking upstream updates.

Negative:

- Lower-level socket operations require manual handling of connection pooling, retries, and protocol framing.
- Advanced features such as circuit breaking and automatic failover must be explicitly authored or layered.

## Alternatives

- Adopting third-party networking and HTTP client frameworks (rejected)
  Rejected because: Third-party network frameworks introduce transitive dependencies, version pinning conflicts for downstream consumers, and larger binary footprints
  When valid: When complex transport protocols such as advanced RPC frameworks or service mesh sidecar integrations are required
- Authoring custom raw socket wrappers outside the net package (rejected)
  Rejected because: Custom socket implementations bypass runtime-optimized non-blocking IO and cross-platform runtime networking abstractions
  When valid: When interacting directly with specialized low-level kernel interfaces or custom hardware protocols

## Risks

- Unconfigured timeouts on standard network dialers can lead to leaked sockets or hung execution routines
  Mitigation: Enforce explicit timeout parameters and context cancellation on all connection attempts
  Owner: Core Engineering Team
- Manual retry and error classification logic may introduce inconsistencies between client and server transport layers
  Mitigation: Provide shared internal transport helper contracts with standardized retry policies
  Owner: Core Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Inspect the project dependency manifest to verify active runtime capabilities and ensure network API compatibility.
- Ensure all network dialer and listener calls wrap connections in appropriate error handling and structured logging.

## Continuation Context

Verify commands:

- Discover and run the project static analysis and linting scripts to verify that network communications use approved standard library packages.
- Discover and execute the project test suite to validate network connection handling, retry mechanisms, and timeout behavior.

Accept when:

- Static analysis confirms all socket dialing and HTTP transport interactions utilize standard library networking packages without unapproved third-party networking imports.
- All unit and integration tests for client and server transport pass without connection leaks or unhandled network errors.

## Enforcement

- Verified by: Automated static analysis and import inspection in continuous integration workflows
- Verified by: Mandatory peer code review for any changes introducing external network dependencies
- Violation handling: Pull requests introducing unauthorized third-party networking libraries will fail automated linting gates and be blocked from merge until remediated
- Exception process: Submit an architectural review request detailing technical justifications where standard library networking cannot meet performance or protocol requirements
