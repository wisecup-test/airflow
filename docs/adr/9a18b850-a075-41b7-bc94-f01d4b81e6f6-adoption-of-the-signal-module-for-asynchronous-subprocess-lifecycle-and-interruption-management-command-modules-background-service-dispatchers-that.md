---
version: 2
name: 9a18b850-a075-41b7-bc94-f01d4b81e6f6-adoption-of-the-signal-module-for-asynchronous-subprocess-lifecycle-and-interruption-management-command-modules-background-service-dispatchers-that
description: "Adoption of the signal Module for Asynchronous Subprocess Lifecycle and Interruption Management: Command Modules Background Service Dispatchers That"
metadata:
  actual:
    adr_id: 9a18b850-a075-41b7-bc94-f01d4b81e6f6
    status: accepted
    candidate_group_id: a8ff5545-6dd5-4cf4-9da1-36325c38f915
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Adoption of the signal Module for Asynchronous Subprocess Lifecycle and Interruption Management: Command Modules Background Service Dispatchers That

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/**`

## Context

- Command-line execution modules and background utility services spawn concurrent subprocess pools and external processes to execute builds, test suites, and analysis tasks.
- Asynchronous execution flows require consistent interception of system interruption signals to prevent orphaned processes, dangling file descriptors, and corrupted runtime states.
- The codebase coordinates child process groups and background servers by binding handlers provided by the standard signal module directly to termination and interruption events.

## Problem Statement

Spawning parallel child processes and background daemons without coordinated signal interception causes leaked processes, system resource exhaustion, and unresponsive command interfaces when executions are interrupted or cancelled. A standardized signal handling and process termination architecture is required to ensure that termination events cleanly propagate to all active child process groups across command dispatchers.

## Decision

- **R-LM-001** MUST: Command modules and background service dispatchers that spawn child processes or worker pools MUST adopt the standard signal module to intercept system termination and interruption signals.

## Policy Block

In scope:

- Command-line modules that initiate parallel subprocesses, worker pools, or background daemon servers.
- Utility routines responsible for orchestrating external process lifecycles and process group cleanup.

Out of scope:

- Pure analytical routines and stateless utility functions that execute synchronously in-process without spawning child processes.
- In-memory data transformation operations that do not allocate external operating system resources.

Exceptions:

- **EXC-20-001** An external process supervisor or container runtime manages child process lifetimes and traps termination signals directly.
  Approval: Architecture Review Board
  Documentation: Document external supervisory signal forwarding configuration within the module header.

## Rationale

- Explicit signal management using the standard signal module provides deterministic cleanup of parallel worker pools and prevents orphaned child processes.
- Standardizing termination signal handling ensures uniform behavior across local development environments and automated execution runners.
- Propagating termination events across process groups avoids resource leakage such as orphaned container instances, zombie processes, and locked system ports.

## Consequences

Positive:

- Guarantees clean shutdown and external resource deallocation across child process hierarchies when tasks are interrupted.
- Prevents lingering background processes from consuming host system capacity after command termination.
- Establishes a uniform, auditable pattern for interruption handling across command dispatchers and background service utilities.

Negative:

- Increases codebase complexity through explicit signal handler registration, error suppression, and teardown management.
- Introduces potential race conditions between child process exit events and process group signaling that require defensive error handling.

## Alternatives

- Rely exclusively on default runtime process termination without custom signal trapping. (rejected)
  Rejected because: Default runtime termination abruptly aborts the parent process without cleaning up spawned child processes, leaving orphaned background tasks and leaked system resources.
  When valid: Valid only in single-threaded command dispatchers that spawn no external child subprocesses or background services.
- Rely solely on exit callbacks without asynchronous signal handler registration. (rejected)
  Rejected because: Exit callbacks are not reliably invoked when processes are terminated by external signals such as immediate interruption or operating system termination events.
  When valid: Valid for in-process memory state cleanup where asynchronous signal interception is unnecessary.

## Risks

- Deadlocks or re-entrancy issues caused by non-reentrant operations executed inside signal handlers.
  Mitigation: Restrict signal handler logic strictly to setting termination flags and issuing non-blocking process group termination calls.
  Owner: engineering team
- Operating system error races when signaling process groups where child processes have already exited.
  Mitigation: Defensively suppress process lookup and operating system errors during teardown execution.
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
- Register signal handlers at the entry boundary of asynchronous task dispatchers and restore previous handlers in teardown paths.
- Coordinate signal handling with exit handlers to ensure cleanup occurs whether termination is triggered by normal completion or an external signal.

## Continuation Context

Verify commands:

- python3 -c 'import signal; assert hasattr(signal, "signal") and hasattr(signal, "SIGTERM")'
- find . -maxdepth 3 -type f \( -name "*test*" -o -name "*lint*" \) -exec echo "Discovered project verification runner: {}" \;

Accept when:

- All automated tests covering signal registration, process group termination, and signal handler teardown execute successfully.
- Static analysis confirms that custom signal handlers suppress benign operating system errors and avoid non-reentrant operations.

## Enforcement

- Verified by: Automated test suites verifying clean child process termination under simulated termination and interrupt signals.
- Verified by: Continuous inspection and static analysis checking for compliant signal handler registration and process group management.
- Violation handling: Pull requests introducing subprocess dispatchers without signal handling and process group termination contracts will be blocked during review.
- Violation handling: Automated test failures caused by orphaned child processes or lingering tasks will trigger build rejection.
- Exception process: Exceptions must be submitted via an architecture review request detailing the alternative process supervisory mechanism and cleanup guarantees.
