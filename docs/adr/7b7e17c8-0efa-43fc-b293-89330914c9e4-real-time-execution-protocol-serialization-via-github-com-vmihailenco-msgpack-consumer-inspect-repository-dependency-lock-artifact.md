---
version: 2
name: 7b7e17c8-0efa-43fc-b293-89330914c9e4-real-time-execution-protocol-serialization-via-github-com-vmihailenco-msgpack-consumer-inspect-repository-dependency-lock-artifact
description: "Real-Time Execution Protocol Serialization via github.com/vmihailenco/msgpack: Consumer Inspect Repository Dependency Lock Artifact"
metadata:
  actual:
    adr_id: 7b7e17c8-0efa-43fc-b293-89330914c9e4
    status: accepted
    candidate_group_id: d0ed4dfb-3880-4d45-a8fa-587564198457
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - cmd/airflow-go-pack/**
      - internal/bundlefooter/**
      - pkg/execution/**
      - pkg/logging/server/**
---

# Real-Time Execution Protocol Serialization via github.com/vmihailenco/msgpack: Consumer Inspect Repository Dependency Lock Artifact

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `cmd/airflow-go-pack/**`
- `internal/bundlefooter/**`
- `pkg/execution/**`
- `pkg/logging/server/**`

## Context

- The execution engine requires synchronous, real-time message exchange between the supervisor process and worker execution runtimes over stream transport channels.
- Real-time communication across process boundaries requires minimal serialization overhead and low-latency message parsing to maintain task execution throughput.
- The codebase establishes dedicated socket channels for execution control and message framing, utilizing structured binary protocols across the runtime boundary.

## Problem Statement

Inter-process communication between the execution supervisor and task workers requires exchanging typed execution messages, status updates, and control signals across stream connections. Unstructured text or verbose textual formats introduce parsing latency and lack strong framing guarantees across real-time socket boundaries, necessitating a compact, structured binary serialization standard.

## Decision

- **R-IP-001** MUST: The consumer MUST inspect the repository dependency lock artifact to determine and verify the exact resolved version of the serialization library before introducing or modifying any message serialization code.

## Policy Block

In scope:

- Inter-process communication between supervisor processes and worker execution runtimes
- Real-time message frame encoding and decoding across stream socket connections
- Structured execution control protocol payloads

Out of scope:

- Static file inspection and archive bundle metadata generation
- Out-of-band diagnostic logging streams that write raw string lines
- Public REST and HTTP API endpoints terminating outside the supervisor runtime boundary

## Rationale

- Adopting github.com/vmihailenco/msgpack provides efficient binary serialization with native support for structured types while maintaining compact message envelopes across streaming sockets.
- Binary message framing ensures clean boundaries for real-time stream readers, preventing message tearing and enabling error handling on unexpected frame types.
- Standardizing on MessagePack allows schema evolution and type decoding without the wire overhead of text-based formats across high-frequency inter-process channels.

## Consequences

Positive:

- Reduces wire overhead and CPU serialization latency for real-time task execution messaging
- Provides strict message framing and structured decoding across inter-process socket boundaries
- Enables strongly typed message dispatch and explicit initial handshake validation

Negative:

- Precludes direct human inspection of on-the-wire protocol payloads without specialized decoding tools
- Requires strict synchronization of message schema structures between supervisor and execution runtimes

## Alternatives

- JSON over stream connections (rejected)
  Rejected because: JSON parsing incurs higher CPU serialization overhead and requires framing delimiter heuristics on continuous stream connections
  When valid: Acceptable for human-readable external HTTP APIs or low-frequency static metadata inspection
- Custom ad-hoc binary serialization protocol (rejected)
  Rejected because: Custom binary wire protocols require bespoke encoder maintenance and introduce higher defect risk compared to standardized MessagePack serialization
  When valid: Acceptable when strict zero-copy constraints or extreme memory bounds dictate raw memory layout alignment

## Risks

- Schema mismatch between supervisor and worker runtimes leading to deserialization errors during execution initialization
  Mitigation: Validate initial frame types and decode payloads into structured IncomingFrame envelopes with explicit error logging before task invocation
  Owner: engineering team
- Stream corruption or truncated frames causing stalled socket reads on the real-time communication channel
  Mitigation: Enforce length-prefixed binary frame headers and context-bounded read timeouts on communication connections
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
- Encapsulate message serialization inside dedicated frame handling components so that transport details remain decoupled from execution business logic.
- Ensure socket reader implementations evaluate frame lengths prior to reading payloads to prevent unbounded memory allocation from corrupted stream data.

## Continuation Context

Verify commands:

- Discover the project test runner configuration from the repository and execute all unit and integration test suites covering the execution protocol package.
- Inspect the project automated check definitions and run static analysis checks validating dependency constraints and API contract compliance.

Accept when:

- All unit tests covering frame encoding, decoding, and socket message exchange pass without errors.
- Integration test suites verify successful initial handshake, frame deserialization, and error reporting between execution server and supervisor mocks.
- Static analysis confirms no unauthorized external serialization libraries are introduced across the real-time communication boundary.

## Enforcement

- Verified by: Continuous integration test suites validating frame encoding and decoding interoperability
- Verified by: Pull request code reviews focusing on real-time socket communication and message schema boundaries
- Violation handling: Pull requests introducing unencoded text streams or non-standard serialization across real-time execution sockets are blocked during code review
- Violation handling: Test failures during frame serialization validation halt build verification in continuous integration
- Exception process: Exceptions for non-MessagePack wire protocols require an architectural RFC detailing transport constraints and sign-off from the runtime architecture maintainers
