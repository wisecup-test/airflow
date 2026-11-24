---
version: 2
name: 001bbe0b-9fc2-401a-b902-aafaee9ed2ae-subprocess-module-adoption-for-system-process-and-external-command-execution-process-executions-capture-standard-output-error
description: "subprocess Module Adoption for System Process and External Command Execution: Process Executions Capture Standard Output Error"
metadata:
  actual:
    adr_id: 001bbe0b-9fc2-401a-b902-aafaee9ed2ae
    status: accepted
    candidate_group_id: d6efc215-c0b0-4dfe-a102-f4a10c857dda
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# subprocess Module Adoption for System Process and External Command Execution: Process Executions Capture Standard Output Error

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Multiple system-level components including security credential renewal and command-line execution entrypoints require invocation and monitoring of operating system processes.
- Executing external binaries without standardized interfaces risks command injection vulnerabilities and inconsistent stream management.
- Relying on standard runtime modules avoids external third-party dependencies while providing consistent process lifecycle management.

## Problem Statement

System utilities and security features frequently require spawning external operating system processes to perform credential renewal and host command coordination. Lacking a uniform architectural pattern for process execution leads to insecure shell invocations, unhandled stream deadlocks, and inconsistent error status reporting across the codebase.

## Decision

- **R-LM-001** SHOULD: Process executions SHOULD capture standard output and standard error streams using dedicated handlers to facilitate structured error reporting.

## Policy Block

In scope:

- Modules and components responsible for dispatching external operating system binaries and managing command-line processes.
- Security integration routines executing external authentication and credential refresh tools.

Out of scope:

- In-memory data processing and native language function invocations that do not cross process boundaries.
- Network client communications executed directly through native application transport protocols.

## Rationale

- The codebase standardizes on the standard library subprocess module across security and command-line packages to ensure zero-dependency process coordination.
- Enforcing structured argument passing and sanitization via shlex.quote prevents command injection vulnerabilities inherent in raw shell execution.
- Standardizing stream redirection and lifecycle monitoring ensures predictable exit code propagation and diagnostic capture.

## Consequences

Positive:

- Ensures process execution relies solely on core standard library facilities without adding third-party dependency overhead.
- Provides programmatic control over process lifecycle, stream redirection, and error exit code propagation across CLI and security subpackages.
- Enables robust parameter quoting to mitigate injection risks when spawning external binaries.

Negative:

- Requires careful manual argument sanitization to eliminate command injection attack vectors.
- Introduces platform-specific process execution semantics and potential pipe buffer deadlock hazards if standard streams are improperly drained.

## Alternatives

- Third-party process orchestration framework (rejected)
  Rejected because: Introduces external dependency bloat and version maintenance overhead when standard library capabilities fulfill process supervision requirements.
  When valid: Valid if cross-node distributed process scheduling or complex daemon lifecycle management beyond single-host execution is required.
- Raw shell script wrappers and operating system native command chains (rejected)
  Rejected because: Circumvents application-level argument sanitization, weakens error propagation, and impedes programmatic stream interception.
  When valid: Valid only during early bootstrap phases prior to runtime initialization.

## Risks

- Command injection vulnerabilities resulting from improperly escaped variable parameters in command vectors.
  Mitigation: Enforce shlex.quote on all variable argument elements and disallow raw shell execution modes.
  Owner: engineering team
- Process deadlocks caused by unconsumed standard output or error pipes filling system buffer limits.
  Mitigation: Utilize dedicated stream reader threads or asynchronous polling to drain output buffers continuously.
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
- Construct all command invocations as structured argument vectors rather than raw concatenated strings.
- Ensure long-running process supervision executes in non-blocking threads with dedicated stream readers to prevent buffer deadlocks.

## Continuation Context

Verify commands:

- Discover and execute the project verification test runner from the root manifest to validate process execution contracts.
- Discover and run the static analysis and security scanning suite from the repository configuration to ensure command vector safety.

Accept when:

- All discovered unit and integration test suites for process invocation execute and terminate with a successful exit status.
- Automated static analysis and security linters pass with zero detected instances of unquoted shell command construction or unsafe process invocations.

## Enforcement

- Verified by: Automated static security scanning and linters configured within continuous integration pipelines.
- Verified by: Peer code review validating adherence to process execution boundaries and input sanitization conventions.
- Violation handling: Pull requests containing unquoted command vectors or direct shell invocations are blocked from merging.
- Violation handling: Violations identified in existing modules must be refactored to use standard subprocess APIs with explicit argument sanitization.
- Exception process: Architectural review and security sign-off documented in the project issue tracker justifying why standard process execution interfaces cannot be used.
