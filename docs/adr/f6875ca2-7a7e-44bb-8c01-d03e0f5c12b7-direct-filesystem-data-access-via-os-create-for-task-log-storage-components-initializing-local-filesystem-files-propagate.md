---
version: 2
name: f6875ca2-7a7e-44bb-8c01-d03e0f5c12b7-direct-filesystem-data-access-via-os-create-for-task-log-storage-components-initializing-local-filesystem-files-propagate
description: "Direct Filesystem Data Access via os.Create for Task Log Storage: Components Initializing Local Filesystem Files Propagate"
metadata:
  actual:
    adr_id: f6875ca2-7a7e-44bb-8c01-d03e0f5c12b7
    status: accepted
    candidate_group_id: 2eeab7fd-3994-484d-aab8-1568352ed490
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - pkg/worker/**
---

# Direct Filesystem Data Access via os.Create for Task Log Storage: Components Initializing Local Filesystem Files Propagate

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `pkg/worker/**`

## Context

- Worker execution components capture task workload output streams to maintain execution diagnostics.
- Runtime configuration determines base destination paths while workload payloads specify task-specific log locations.
- File system directories and file descriptors are initialized on demand before workload execution begins.

## Problem Statement

Workload execution output must be reliably persisted and isolated per task instance without causing system crashes or log loss during network unavailability, requiring disciplined filesystem path creation and error handling.

## Decision

- **R-DAP-001** MUST: Components initializing local filesystem files MUST propagate and handle filesystem creation errors instead of panicking or failing silently.

## Policy Block

In scope:

- Worker execution routines initializing output storage for running workloads.
- Filesystem data access operations derived from dynamic task metadata and runtime configuration.

Out of scope:

- Remote telemetry transport and metrics reporting that do not access local disk storage.
- In-memory buffering operations that do not persist data to filesystem descriptors.

## Rationale

- Direct local file creation provides reliable isolation of task run logs without external storage dependencies.
- Dynamic directory path construction based on configuration prevents collision among concurrent workload instances.
- Pattern significance is high within the component but observed across only a single file, indicating a localized data access implementation rather than a shared cross-cutting data access framework.

## Consequences

Positive:

- Workload output persistence is decoupled from immediate central service availability.
- Task execution logs remain partitioned and accessible on the local filesystem by task instance identifier.

Negative:

- Worker hosts require available local storage capacity and appropriate directory creation permissions.
- File descriptor management requires explicit lifecycle handling to prevent descriptor exhaustion during prolonged executions.

## Alternatives

- Direct remote network log streaming without local disk persistence (rejected)
  Rejected because: Network partitions or server unavailability during workload execution can cause complete loss of diagnostic logs.
  When valid: Network streaming infrastructure guarantees high availability with local memory-bounded buffering.
- Standard output logging exclusively without file descriptor creation (rejected)
  Rejected because: Prevents workload-specific log isolation and complicates retrieval of individual task instance run outputs.
  When valid: Workloads execute in fully containerized environments where host engines capture and route standard output streams.

## Risks

- Local storage exhaustion on worker nodes executing high volumes of verbose tasks.
  Mitigation: Implement disk space monitoring and log retention rotation policies on worker nodes.
  Owner: engineering team
- Workload failure when destination log directories lack write permissions or encounter path collisions.
  Mitigation: Enforce pre-creation directory verification and validate write permissions before task initialization.
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
- Ensure directory creation precedes file handle initialization using secure permission bits that restrict unauthorized access.
- Combine file handlers with parent logger handlers using tee constructs when streaming to both file storage and supervisor outputs is enabled.

## Continuation Context

Verify commands:

- Discover and execute the repository test suite to verify worker execution and task log initialization routines.
- Search the codebase for direct filesystem creation calls to confirm error handling and directory permission standards are maintained.

Accept when:

- Local file persistence routines successfully create designated storage paths and handle permission errors gracefully.
- Task log stream handlers write output to created file descriptors without panics or resource leaks.
- Verification routines confirm absence of hardcoded file system destinations across worker execution logic.

## Enforcement

- Verified by: Automated continuous integration test suites verifying data access boundaries.
- Verified by: Peer code review confirming proper directory permission masks and error handling on file allocation.
- Violation handling: Pull requests introducing unhandled filesystem errors or hardcoded destination paths are blocked from merging.
- Violation handling: Static analysis findings highlighting direct unconfigured filesystem access must be remediated.
- Exception process: Submit an architectural variance request detailing alternate log transport mechanisms to the platform engineering team.
- Exception process: Obtain formal approval prior to bypassing local file system data access constraints.
