---
version: 2
name: cross-cutting-execution-runtime-integration-external-c-9a5c
description: "CoordinatorComm Integration Protocol: Execution Runtime Integration External Coordinator Use"
metadata:
  actual:
    adr_id: 9a5cc329-cd94-4206-911f-4ef056618459
    status: accepted
    candidate_group_id: d57000cc-a411-434d-a874-b8cafdc9953b
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/execution/**
---

# CoordinatorComm Integration Protocol: Execution Runtime Integration External Coordinator Use

Scope: applies to `go-sdk/pkg/execution/**`.

These rules are ALWAYS ACTIVE for:
- Execution runtime integration components communicating with coordinator or supervisor processes over stream connections
- Modules handling structured request-response frame multiplexing and connection lifecycle management

### Rules

- **R-TS-001** MUST: All execution runtime integration with the external coordinator MUST use CoordinatorComm to manage frame exchanges, dispatch loops, and request-response multiplexing.

### Verify

- Discover and run the project test suite covering execution communication and integration dispatcher behavior.
- Discover and run the project concurrency and race detector verification scripts to validate thread safety under canceled contexts.

**Accept when:**

- Integration test suites verify that concurrent Communicate calls correctly receive paired response frames without data corruption.
- Cancellation test suites verify that canceled contexts release pending waiter registrations and do not leave partial frames on the connection.
- Direct ReadMessage invocation after dispatcher initialization fails deterministically with an invalid state error.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration test execution including race detection suites
- Verified by: Architectural and peer code reviews for execution transport modules
- Violation handling: Pull requests bypassing CoordinatorComm for stream frame dispatch will be rejected during review and automated validation.
- Violation handling: Direct socket deadline manipulations or unframed reads in execution paths will be flagged as blocking issues.
- Exception process: Submit an architectural review request detailing why custom stream management is required and demonstrating frame boundary safety under cancellation.
- Exception process: Requires approval from runtime infrastructure leads and verification of protocol compatibility.
</enforcement>
