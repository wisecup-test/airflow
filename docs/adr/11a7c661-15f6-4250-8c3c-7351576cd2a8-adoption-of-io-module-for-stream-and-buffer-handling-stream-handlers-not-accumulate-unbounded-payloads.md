---
version: 2
name: 11a7c661-15f6-4250-8c3c-7351576cd2a8-adoption-of-io-module-for-stream-and-buffer-handling-stream-handlers-not-accumulate-unbounded-payloads
description: "Adoption of io Module for Stream and Buffer Handling: Stream Handlers Not Accumulate Unbounded Payloads"
metadata:
  actual:
    adr_id: 11a7c661-15f6-4250-8c3c-7351576cd2a8
    status: accepted
    candidate_group_id: b65dc43f-90c3-41cf-81ba-10ebde282069
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of io Module for Stream and Buffer Handling: Stream Handlers Not Accumulate Unbounded Payloads

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Command-line utilities and configuration formatters require flexible output destinations including standard console streams and exported target files
- Task logging handlers require reliable stream emission and flushing mechanisms across process boundaries and remote storage backends
- The codebase uses standard library stream facilities across CLI tools and logging task handlers to standardize I/O handling

## Problem Statement

Directly coupling command-line formatters, data exporters, and task logging components to concrete process output streams or filesystem paths hinders testability and prevents flexible redirection of output. The architecture requires a consistent stream abstraction that enables uniform in-memory, file-based, and standard stream handling across core subsystems.

## Decision

- **R-LM-001** SHOULD_NOT: Stream handlers SHOULD NOT accumulate unbounded stream payloads in memory buffers when the total payload size is indeterminate.

## Policy Block

In scope:

- Command-line interface data exporters, configuration formatting handlers, and task logging handlers performing stream I/O operations

Out of scope:

- Pure data transformation routines and database query execution components with no stream input or output requirements

## Rationale

- Standardizing on core io stream interfaces ensures consistent stream handling across CLI utilities and log handlers without introducing third-party dependencies
- Abstract stream contracts allow unit tests to execute against isolated in-memory buffers rather than mutating process-level streams or filesystem state
- Explicit stream flushing and contextual management provide predictable resource cleanup and prevent partial write loss

## Consequences

Positive:

- Decouples export, configuration, and logging routines from physical files and console devices
- Facilitates automated testing through the injection of in-memory stream buffers
- Establishes consistent stream handling semantics across command-line and task execution subsystems

Negative:

- Requires callers to explicitly manage stream lifecycles and execute buffer flushes to avoid incomplete writes
- Incurs potential memory pressure if high-volume log or export streams are buffered entirely in memory

## Alternatives

- Direct String Concatenation and File Path Writing (rejected)
  Rejected because: Couples data producing logic directly to filesystem paths and process output, making testing and stream redirection difficult
  When valid: Simple one-off scripts with no requirement for stream redirection or testability
- Third-Party Streaming Abstraction Frameworks (rejected)
  Rejected because: Introduces unnecessary external dependencies for capabilities already provided natively by standard core interfaces
  When valid: Environments requiring distributed asynchronous stream processing across distributed network topologies
- Standard io Module Stream Abstractions (accepted)
  Rejected because: None
  When valid: Components producing or consuming text and byte streams across CLI, configuration, and logging boundaries

## Risks

- Unclosed stream handles leading to file descriptor or resource leaks
  Mitigation: Enforce contextual lifecycle management and explicit stream closing in all stream-handling routines
  Owner: engineering team
- Excessive memory consumption when buffering large task logs or export datasets in memory
  Mitigation: Stream large payloads in chunks rather than buffering entire payloads in in-memory stream objects
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
- Implementations must prefer accepting abstract stream interfaces over concrete file objects to support both in-memory and on-disk stream targets
- Ensure all stream writers wrap operations in contextual management blocks or explicitly invoke stream flushing upon write completion

## Continuation Context

Verify commands:

- sh -c 'test -n "$(git status --porcelain 2>/dev/null)" || echo "Repository state checked; locate test execution scripts to validate stream components"'
- echo "Discover and execute project test suite from repository configuration scripts to verify stream contracts"

Accept when:

- Stream consumer components accept polymorphic io stream targets and pass automated test assertions with in-memory streams
- Log emission and CLI export routines flush stream buffers without unhandled resource warnings

## Enforcement

- Verified by: Static analysis rules detecting unbuffered or unclosed stream operations
- Verified by: Peer review of pull requests touching CLI output, configuration formatting, or logging handlers
- Verified by: Automated test suites exercising stream-based components with in-memory stream substitutes
- Violation handling: Pull requests directly coupling business logic to standard process outputs without stream abstractions are blocked at code review
- Violation handling: Stream leaks or unhandled flush operations identified during static analysis must be resolved before deployment
- Exception process: Submit an architectural exception request documenting the specific requirement preventing the use of standard stream abstractions
- Exception process: Obtain approval from component maintainers prior to merging non-compliant I/O implementations
