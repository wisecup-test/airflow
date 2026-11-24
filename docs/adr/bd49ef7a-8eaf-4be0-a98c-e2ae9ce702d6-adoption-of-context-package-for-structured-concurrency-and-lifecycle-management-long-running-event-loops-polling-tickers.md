---
version: 2
name: bd49ef7a-8eaf-4be0-a98c-e2ae9ce702d6-adoption-of-context-package-for-structured-concurrency-and-lifecycle-management-long-running-event-loops-polling-tickers
description: "Adoption of context Package for Structured Concurrency and Lifecycle Management: Long Running Event Loops Polling Tickers"
metadata:
  actual:
    adr_id: bd49ef7a-8eaf-4be0-a98c-e2ae9ce702d6
    status: accepted
    candidate_group_id: 61c40993-d5e6-45b1-b980-4a9bffb54265
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - edge/**
      - example/bundle/**
      - pkg/execution/**
      - pkg/logging/server/**
      - pkg/worker/**
---

# Adoption of context Package for Structured Concurrency and Lifecycle Management: Long Running Event Loops Polling Tickers

Date: 2025-05-15
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `edge/**`
- `example/bundle/**`
- `pkg/execution/**`
- `pkg/logging/server/**`
- `pkg/worker/**`

## Context

- Long-running worker routines, communication dispatchers, and embedded servers require deterministic cancellation and graceful shutdown mechanisms when termination signals or heartbeat failures occur.
- Uncoordinated background routines risk leaking system resources, leaving dangling network streams, or corrupting state when asynchronous tasks terminate abruptly.
- Multiplexed request-response protocols over persistent connections require decoupling between message dispatchers and concurrent callers to handle cancellations without stream poisoning.
- Parallel sub-task executions require unified synchronization primitives to ensure all child operations finish before aggregating results.

## Problem Statement

Asynchronous worker loops, network servers, and coordinator communication dispatchers must process ongoing workloads while remaining responsive to external interruption, heartbeat timeouts, and cancellation signals. Without a standardized concurrency and cancellation model, uncoordinated goroutines can leak resources, block indefinitely on network operations, or terminate prematurely during critical task execution without completing necessary drainage and cleanup steps.

## Decision

- **R-PS-001** MUST: Long-running event loops and polling tickers MUST evaluate Context cancellation within multiplexed select statements alongside work channels and timer channels.

## Policy Block

In scope:

- All asynchronous worker daemon routines, background polling loops, and heartbeat runners.
- Network communication coordinators, frame dispatchers, and embedded HTTP log servers.
- Concurrent sub-task execution routines requiring parallel fan-out and result aggregation.

Out of scope:

- Synchronous in-memory utility functions that execute deterministically without blocking operations.
- Pure data transformation and serialization functions without asynchronous workflows.

## Rationale

- Context propagation provides standardized cancellation signaling across caller and callee boundaries without requiring custom termination flags.
- Channel multiplexing via select allows event loops to process operational events and termination signals concurrently without thread starvation.
- Bounded shutdown contexts prevent server routines from hanging indefinitely during process termination while allowing active operations to drain safely.
- WaitGroup synchronization ensures deterministic fan-out execution and structured lifecycle management for concurrent child operations.

## Consequences

Positive:

- Enforces predictable cancellation propagation and automated resource cleanup across worker daemons and network dispatchers.
- Eliminates orphaned background routines and dangling socket connections upon task termination or worker shutdown.
- Supports safe graceful draining of running workloads when termination signals are received.
- Provides deterministic parallel execution and error aggregation for concurrent child operations.

Negative:

- Requires threading Context parameters through all asynchronous function signatures and communication interfaces.
- Increases implementation complexity when tracking pending request maps and synchronization locks during asynchronous message dispatch.
- Introduces risks of goroutine leaks if Context cancellation channels are omitted from blocking select cases.

## Alternatives

- Unsynchronized goroutines with shared global termination boolean flags (rejected)
  Rejected because: Global boolean flags do not integrate with channel multiplexing in select statements, cannot interrupt blocking network transmissions, and introduce data races without explicit locking.
  When valid: Single-threaded execution loops devoid of blocking network or channel operations.
- Synchronous sequential processing without asynchronous dispatch or parallel worker execution (rejected)
  Rejected because: Serializing worker heartbeats, task fetching, and job execution causes heartbeats to miss remote deadlines during long-running tasks, causing premature worker eviction.
  When valid: Simple batch execution scripts with strictly sequential single-item processing.

## Risks

- Goroutines blocking on unbuffered channel writes or stuck socket reads after Context cancellation.
  Mitigation: Ensure transmission channels are appropriately buffered and that cancellation select branches remove pending registrations.
  Owner: engineering team
- Premature process exit before active tasks drain completely during shutdown sequences.
  Mitigation: Implement bounded shutdown timeouts and wait group synchronization before exiting service loops.
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
- Always ensure cancellation functions returned by WithTimeout or WithCancel are invoked via deferred calls to avoid Context leaks.
- Protect shared pending request maps with mutual exclusion locks before dispatching asynchronous requests and verify registrations before read loops deliver responses.
- In long-running worker loops, separate graceful drain initiation from immediate termination to permit active workloads to complete within configured grace periods.

## Continuation Context

Verify commands:

- Discover the project test runner configuration from the repository manifest and execute all unit and concurrency integration tests.
- Discover and run the project static analysis and race detector tools specified in the build scripts to verify absence of data races in asynchronous dispatchers.

Accept when:

- All unit and integration test suites pass with zero concurrency race conditions reported by static analysis and race detection tools.
- Worker routines, communication dispatchers, and server instances terminate cleanly within timeout boundaries upon Context cancellation without leaking goroutines.

## Enforcement

- Verified by: Continuous integration pipelines executing concurrency test suites and race detection analysis.
- Verified by: Mandatory peer code review verifying Context propagation and proper channel select handling on all asynchronous routines.
- Violation handling: Continuous integration failure blocking merge for any code introducing goroutine leaks or race conditions.
- Violation handling: Rejection of pull requests that omit Context parameters from asynchronous public interfaces or ignore cancellation signals.
- Exception process: Submit an architectural review request documenting why an asynchronous routine cannot accept Context and detailing alternate deterministic lifecycle management.
