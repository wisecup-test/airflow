---
version: 2
name: 0d482a57-a108-4800-881f-7db37c7c9035-google-golang-org-grpc-remote-procedure-call-integration-high-level-domain-clients-wrap-low
description: "google.golang.org/grpc Remote Procedure Call Integration: High Level Domain Clients Wrap Low"
metadata:
  actual:
    adr_id: 0d482a57-a108-4800-881f-7db37c7c9035
    status: accepted
    candidate_group_id: 18de2962-f355-48c9-8bce-e40459c59a7a
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - bundle/bundlev1/bundlev1client/**
      - internal/protov1/**
---

# google.golang.org/grpc Remote Procedure Call Integration: High Level Domain Clients Wrap Low

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `bundle/bundlev1/bundlev1client/**`
- `internal/protov1/**`

## Context

- Inter-process communication between host runners and external bundle execution units requires a strongly typed, performant remote procedure call protocol.
- The codebase adopts google.golang.org/grpc to standardize client and server communication contracts across isolated process boundaries.
- Protocol-generated service contracts define standardized methods for bundle metadata retrieval and workload execution.
- Structured error handling using canonical remote procedure call status codes is necessary to distinguish protocol failures from operational task errors.

## Problem Statement

Direct in-process coupling between execution runners and bundle implementations restricts isolation, lifecycle independence, and extensibility across process boundaries. Without a standardized, high-performance remote procedure call framework and strongly typed interface contracts, inter-process communication risks serialization inconsistencies, lack of context-aware cancellation, and uncoordinated failure handling.

## Decision

- **R-LM-001** SHOULD: High-level domain clients SHOULD wrap low-level remote procedure call responses into domain-specific data structures rather than leaking generated transport types across application boundaries.

## Policy Block

In scope:

- Components implementing remote inter-process plugin contracts and bundle communication layers
- Service endpoints and client adapters executing across decoupled runtime processes

Out of scope:

- Internal intra-process function invocations and pure domain computations
- Local utility packages operating within the same memory space

Exceptions:

- **EXP-20-001** In-process mock adapters are required for testing without network or process socket overhead
  Approval: Architecture review board and module maintainer approval
  Documentation: Record the architectural variance in the component test documentation

## Rationale

- Evidence from bundle client and protocol modules demonstrates active integration with google.golang.org/grpc, confirming the adoption of structured remote procedure calls over ad-hoc inter-process communication.
- Using google.golang.org/grpc provides standardized context propagation for cancellation, deadline enforcement, and strongly typed payload definitions.
- Canonical status codes from google.golang.org/grpc/codes and constructors from google.golang.org/grpc/status provide predictable error classification across process boundaries.

## Consequences

Positive:

- Decouples execution environments across process boundaries through strongly typed remote procedure call contracts.
- Enforces deterministic cancellation and deadline propagation through standard context handling.
- Standardizes error handling and status code reporting across distributed execution components.

Negative:

- Introduces operational complexity associated with managing remote procedure call client connections and lifecycle states.
- Requires compilation and maintenance of protocol buffer interface bindings across service definitions.
- Adds serialization and deserialization overhead compared to direct in-memory invocation.

## Alternatives

- Standard input and output stream serialization using custom text protocols (rejected)
  Rejected because: Custom stream serialization lacks standardized type safety, built-in context propagation, and efficient binary framing
  When valid: Lightweight command-line utilities where external transport dependencies must be avoided
- HTTP REST endpoints using JSON payloads (rejected)
  Rejected because: REST over standard HTTP introduces higher latency and lacks native multiplexed streaming and strongly typed contract code generation
  When valid: Public-facing external web application programming interfaces requiring generic browser access

## Risks

- Connection drops or stalled remote procedure calls may hang client workflows without proper timeouts.
  Mitigation: Enforce context deadlines on every remote procedure call invocation.
  Owner: Engineering Team
- Generated contract drift between client and server components causing serialization mismatch.
  Mitigation: Ensure automated protocol verification during repository test workflows and build steps.
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
- Ensure that all client invocations wrap low-level error returns to preserve domain abstractions.
- Configure client connections with appropriate static call options and interceptors to maintain observability and logging.

## Continuation Context

Verify commands:

- Discover and run the project's dependency verification script to confirm that the resolved remote procedure call library aligns with the repository lock artifact.
- Discover and execute the project's static analysis suite to verify compliance with remote procedure call client and server registration standards.
- Discover and run the test suite covering bundle client communication to ensure remote procedure calls succeed under defined context deadlines.

Accept when:

- All inter-process bundle client operations resolve successfully via the configured remote procedure call transport.
- Remote calls gracefully handle timeout and cancellation when context cancellation triggers.
- Static analysis and repository test suites pass without reported violations or unimplemented stubs.

## Enforcement

- Verified by: Automated continuous integration pipeline executing repository test and linting suites.
- Verified by: Peer code review verifying adherence to remote procedure call contract boundaries and error handling rules.
- Violation handling: Pull requests violating remote procedure call registration or context propagation rules must be blocked from merging until corrected.
- Violation handling: Unregistered service methods or improper status code usage flagged during automated checks trigger build failure.
- Exception process: Formal request submitted to the architecture review team detailing rationale and test isolation proof for alternative transport.
- Exception process: Document approved exceptions in the repository decision log.
