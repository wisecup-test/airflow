---
version: 2
name: cross-cutting-execution-handlers-pass-string-represent-7c27
description: "runtime/debug Adoption for Execution Panic Diagnostics and Stack Recovery: Execution Handlers Pass String Representation Debug"
metadata:
  actual:
    adr_id: 7c27b44b-6667-4fb7-a55d-95e48eb1e3fb
    status: accepted
    candidate_group_id: ec576904-fb0a-4847-bff9-79b4587a571a
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/execution/**
---

# runtime/debug Adoption for Execution Panic Diagnostics and Stack Recovery: Execution Handlers Pass String Representation Debug

Scope: applies to `go-sdk/pkg/execution/**`.

These rules are ALWAYS ACTIVE for:
- Task execution runners and task lifecycle handlers
- Metadata processing, dumping, and parsing components within the execution subsystem

### Rules

- **R-LM-001** MUST: Execution handlers MUST pass the string representation of debug.Stack into structured logging calls alongside error details and task context attributes.

### Verify

- Discover and execute the project test runner to validate panic recovery and stack capture behavior across execution packages
- Discover and execute the project static analyzer to verify that execution panic recovery handlers invoke runtime/debug stack capture APIs

**Accept when:**

- All task runner panic recovery paths extract and format stack traces via runtime/debug without process termination
- Structured log outputs for recovered panics contain non-empty stack trace payloads along with task instance identifiers
- Project test verification suites covering execution recovery mechanisms pass completely without unhandled panics

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated unit and integration test assertions verifying panic recovery behavior in task runners
- Verified by: Peer code review checks on error recovery blocks within execution workflows
- Verified by: Static analysis inspection ensuring recovery handlers reference runtime/debug stack inspection APIs
- Violation handling: Code review rejection for any execution recovery block that catches panics without extracting stack traces
- Violation handling: CI test failure when simulated panic scenarios fail to record structured stack telemetry
- Exception process: Submit an architectural review request justifying why a panic recovery path cannot or should not materialize call stacks
- Exception process: Obtain sign-off from the execution architecture maintainers before bypassing runtime diagnostic capture
</enforcement>
