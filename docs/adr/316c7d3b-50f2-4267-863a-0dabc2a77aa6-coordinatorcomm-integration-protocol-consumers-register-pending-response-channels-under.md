---
version: 2
name: 316c7d3b-50f2-4267-863a-0dabc2a77aa6-coordinatorcomm-integration-protocol-consumers-register-pending-response-channels-under
description: "CoordinatorComm Integration Protocol: Consumers Register Pending Response Channels Under"
metadata:
  actual:
    adr_id: 316c7d3b-50f2-4267-863a-0dabc2a77aa6
    status: accepted
    candidate_group_id: d57000cc-a411-434d-a874-b8cafdc9953b
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/execution/**
---

# CoordinatorComm Integration Protocol: Consumers Register Pending Response Channels Under

Date: 2025-05-19
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `pkg/execution/**`

## Context

- Execution runtime integration requires reliable, bidirectional frame multiplexing between worker processes and an external coordinator supervisor over streaming connections.
- Context cancellations and concurrent caller invocations can interrupt active transmissions, creating the risk of partial frame writes or stale deadline poisoning on shared connections.
- CoordinatorComm separates request dispatching from synchronous stream reads using a dedicated dispatcher loop, thread-safe waiter registration, and background transmission goroutines.
- Standardizing response payload access through frame body structures and unified error decoding prevents protocol divergence across execution server integration points.

## Problem Statement

Uncoordinated stream writes and direct synchronous reads across concurrent execution callers risk frame corruption, deadlocks, and orphan response channels when client operations are canceled or interrupted. A structured communication dispatcher is required to guarantee frame boundary preservation, deterministic response routing, and unified error handling across runtime integration boundaries.

## Decision

- **R-TS-001** MUST: Consumers MUST register pending response channels under the communication mutex before transmitting requests to prevent race conditions with incoming dispatcher deliveries.

## Policy Block

In scope:

- Execution runtime integration components communicating with coordinator or supervisor processes over stream connections
- Modules handling structured request-response frame multiplexing and connection lifecycle management

Out of scope:

- Unframed raw data streams that do not participate in coordinator frame protocols
- Stateless one-way log transmission streams handled outside coordinator frame dispatch

## Rationale

- Routing all framed coordinator exchanges through CoordinatorComm guarantees that concurrent requests maintain distinct waiter identifiers without race conditions against the dispatcher loop.
- Background send routines prevent connection poisoning and truncated frames during context cancellation, ensuring subsequent operations on the connection remain structurally valid.
- Centralized error decoding into ApiError preserves protocol consistency across initial handshake reads and steady-state request-response cycles.

## Consequences

Positive:

- Guarantees wire frame integrity by preventing partial writes during context cancellation.
- Eliminates message delivery races between caller channel registration and dispatcher reading.
- Unifies protocol error response handling across initialization and streaming phases.

Negative:

- Context-canceled send operations consume background routine resources until completing or terminating on connection failure.
- Unmatched coordinator responses arriving after caller cancellation must be discarded by the dispatcher, adding minor processing overhead.

## Alternatives

- Direct synchronous stream read and write without an asynchronous dispatcher loop (rejected)
  Rejected because: Synchronous reads on a shared connection block concurrent requests and prevent deterministic multiplexing across callers.
  When valid: Simple point-to-point connections with strictly sequential, single-threaded request-response patterns.
- Connection-level cancellation via write deadline mutation or immediate socket termination (rejected)
  Rejected because: Mutating deadlines mid-write risks partial length-prefixed frame transmission and poisons subsequent operations on the stream.
  When valid: Transports with native frame multiplexing or where connections are single-use and discarded immediately on cancellation.

## Risks

- Proliferation of background send routines under rapid caller cancellation cascades if the underlying stream stalls.
  Mitigation: Monitor connection health and terminate the connection at the transport level when the dispatcher encounters unrecoverable read errors.
  Owner: engineering team
- Direct invocation of sequential read APIs after dispatcher initialization causing protocol errors.
  Mitigation: Enforce state checks that immediately reject sequential reads once the dispatcher has begun execution.
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
- Initialize CoordinatorComm prior to launching asynchronous server routines and ensure initial bundle registration completes before starting the continuous read dispatcher.
- Ensure waiter channel deletion occurs in caller cleanup blocks or cancellation branches to prevent waiter map accumulation on timed-out requests.

## Continuation Context

Verify commands:

- Discover and run the project test suite covering execution communication and integration dispatcher behavior.
- Discover and run the project concurrency and race detector verification scripts to validate thread safety under canceled contexts.

Accept when:

- Integration test suites verify that concurrent Communicate calls correctly receive paired response frames without data corruption.
- Cancellation test suites verify that canceled contexts release pending waiter registrations and do not leave partial frames on the connection.
- Direct ReadMessage invocation after dispatcher initialization fails deterministically with an invalid state error.

## Enforcement

- Verified by: Continuous integration test execution including race detection suites
- Verified by: Architectural and peer code reviews for execution transport modules
- Violation handling: Pull requests bypassing CoordinatorComm for stream frame dispatch will be rejected during review and automated validation.
- Violation handling: Direct socket deadline manipulations or unframed reads in execution paths will be flagged as blocking issues.
- Exception process: Submit an architectural review request detailing why custom stream management is required and demonstrating frame boundary safety under cancellation.
- Exception process: Requires approval from runtime infrastructure leads and verification of protocol compatibility.
