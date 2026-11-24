---
version: 2
name: cross-cutting-callers-not-mutate-connection-deadlines-c043
description: "CoordinatorComm Integration Protocol: Callers Not Mutate Connection Deadlines Close"
metadata:
  actual:
    adr_id: c043002f-4173-44a7-8ccc-aa8428e04249
    status: accepted
    candidate_group_id: d57000cc-a411-434d-a874-b8cafdc9953b
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/execution/**
---

# CoordinatorComm Integration Protocol: Callers Not Mutate Connection Deadlines Close

Scope: applies to `go-sdk/pkg/execution/**`.

These rules are ALWAYS ACTIVE for:
- Execution runtime integration components communicating with coordinator or supervisor processes over stream connections
- Modules handling structured request-response frame multiplexing and connection lifecycle management

### Rules

- **R-TS-001** MUST_NOT: Callers MUST NOT mutate connection deadlines or close the underlying connection mid-write during context cancellation, allowing active send routines to complete in the background to preserve wire frame alignment.

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
