---
version: 2
name: cross-cutting-consumer-inspect-repository-dependency-l-7b7e
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

Scope: applies to `go-sdk/cmd/airflow-go-pack/**`, `go-sdk/internal/bundlefooter/**`, `go-sdk/pkg/execution/**`, `go-sdk/pkg/logging/server/**`.

These rules are ALWAYS ACTIVE for:
- Inter-process communication between supervisor processes and worker execution runtimes
- Real-time message frame encoding and decoding across stream socket connections
- Structured execution control protocol payloads

### Rules

- **R-IP-001** MUST: The consumer MUST inspect the repository dependency lock artifact to determine and verify the exact resolved version of the serialization library before introducing or modifying any message serialization code.

### Verify

- Discover the project test runner configuration from the repository and execute all unit and integration test suites covering the execution protocol package.
- Inspect the project automated check definitions and run static analysis checks validating dependency constraints and API contract compliance.

**Accept when:**

- All unit tests covering frame encoding, decoding, and socket message exchange pass without errors.
- Integration test suites verify successful initial handshake, frame deserialization, and error reporting between execution server and supervisor mocks.
- Static analysis confirms no unauthorized external serialization libraries are introduced across the real-time communication boundary.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration test suites validating frame encoding and decoding interoperability
- Verified by: Pull request code reviews focusing on real-time socket communication and message schema boundaries
- Violation handling: Pull requests introducing unencoded text streams or non-standard serialization across real-time execution sockets are blocked during code review
- Violation handling: Test failures during frame serialization validation halt build verification in continuous integration
- Exception process: Exceptions for non-MessagePack wire protocols require an architectural RFC detailing transport constraints and sign-off from the runtime architecture maintainers
</enforcement>
