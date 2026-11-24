---
version: 2
name: 6d98ae91-8f2c-43b7-a750-ed7c518d5374-runtime-debug-adoption-for-execution-panic-diagnostics-and-stack-recovery-consumers-inspect-workspace-dependency-configuration-discover
description: "runtime/debug Adoption for Execution Panic Diagnostics and Stack Recovery: Consumers Inspect Workspace Dependency Configuration Discover"
metadata:
  actual:
    adr_id: 6d98ae91-8f2c-43b7-a750-ed7c518d5374
    status: accepted
    candidate_group_id: ec576904-fb0a-4847-bff9-79b4587a571a
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/execution/**
---

# runtime/debug Adoption for Execution Panic Diagnostics and Stack Recovery: Consumers Inspect Workspace Dependency Configuration Discover

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `pkg/execution/**`

## Context

- Task execution and metadata handling components require deterministic diagnostic capture when runtime panics occur
- Standard error logging without stack inspection leaves runtime panics without originating file or call site context
- The execution subsystem standardizes on core runtime inspection capabilities to recover from panics and log structured call stacks

## Problem Statement

Unhandled runtime panics in execution tasks can crash worker processes without emitting actionable diagnostic information to coordinating supervisors. Without standardized call stack capture at panic boundaries, operators and developers cannot identify the root cause or execution state that triggered the failure.

## Decision

- **R-LM-001** MUST: Consumers MUST inspect the workspace dependency configuration to discover the resolved runtime specifications and module dependencies prior to compilation.

## Policy Block

In scope:

- Task execution runners and task lifecycle handlers
- Metadata processing, dumping, and parsing components within the execution subsystem

Out of scope:

- Pure utility functions that do not manage execution boundaries or long-running tasks
- Components where process termination is managed by an external orchestrator supervisor without internal panic trapping

## Rationale

- Evidence demonstrates runtime/debug usage paired with structured error logging across multiple execution components
- Utilizing debug.Stack ensures consistent, human-readable call stacks are preserved inside structured logs without third-party stack-unwinding dependencies
- Standardizing on core runtime diagnostics isolates panic handling to the task boundary, protecting supervisor stability

## Consequences

Positive:

- Guarantees deterministic stack trace capture whenever unexpected runtime panics occur in task execution
- Preserves orchestrator stability by preventing task panics from abruptly terminating long-running worker processes
- Correlates detailed stack frames directly with structured execution context attributes in error telemetry

Negative:

- Materializing stack traces via debug.Stack incurs CPU and memory allocation overhead during panic recovery sequences
- Large stack traces increase logging payload sizes and telemetry ingestion costs in high-frequency failure scenarios

## Alternatives

- Relying exclusively on default process crash dumps and operating system standard error streams (rejected)
  Rejected because: Default crash dumps terminate the entire runner process, preventing structured error emission to coordinators and suppressing graceful lifecycle cleanup
  When valid: Applicable only in ephemeral single-shot command-line utilities where immediate process death is acceptable
- Capturing panic values without call stack inspection (rejected)
  Rejected because: Panic error values without stack traces discard the execution call site, making diagnosis of runtime faults and nil pointer dereferences difficult
  When valid: Applicable when panic payloads already contain exhaustive structured stack frames

## Risks

- Excessive string allocations when materializing debug.Stack on high-frequency failure paths
  Mitigation: Format and emit stack traces only within exception and panic recovery pathways, keeping the happy path allocation-free
  Owner: engineering team
- Stack traces exceeding maximum allowable message sizes in structured logging collectors
  Mitigation: Ensure log sinks and message queues enforce reasonable message size caps or truncate oversized stack strings safely
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
- Ensure debug.Stack output is converted to string and mapped to a dedicated stack attribute within structured log calls
- Maintain deferred recovery handlers at the outermost boundary of task execution to safeguard against any task-level crash

## Continuation Context

Verify commands:

- Discover and execute the project test runner to validate panic recovery and stack capture behavior across execution packages
- Discover and execute the project static analyzer to verify that execution panic recovery handlers invoke runtime/debug stack capture APIs

Accept when:

- All task runner panic recovery paths extract and format stack traces via runtime/debug without process termination
- Structured log outputs for recovered panics contain non-empty stack trace payloads along with task instance identifiers
- Project test verification suites covering execution recovery mechanisms pass completely without unhandled panics

## Enforcement

- Verified by: Automated unit and integration test assertions verifying panic recovery behavior in task runners
- Verified by: Peer code review checks on error recovery blocks within execution workflows
- Verified by: Static analysis inspection ensuring recovery handlers reference runtime/debug stack inspection APIs
- Violation handling: Code review rejection for any execution recovery block that catches panics without extracting stack traces
- Violation handling: CI test failure when simulated panic scenarios fail to record structured stack telemetry
- Exception process: Submit an architectural review request justifying why a panic recovery path cannot or should not materialize call stacks
- Exception process: Obtain sign-off from the execution architecture maintainers before bypassing runtime diagnostic capture
