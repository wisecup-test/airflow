---
version: 2
name: 3f437306-3263-49da-8353-936db3bfce33-subprocess-module-adoption-for-external-process-execution-implementations-not-invoke-subprocess-calls-shell
description: "Subprocess Module Adoption for External Process Execution: Implementations Not Invoke Subprocess Calls Shell"
metadata:
  actual:
    adr_id: 3f437306-3263-49da-8353-936db3bfce33
    status: accepted
    candidate_group_id: f6e020fc-2d38-4848-89c2-11762cf6fe79
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# Subprocess Module Adoption for External Process Execution: Implementations Not Invoke Subprocess Calls Shell

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/**`
- `src/tests_common/test_utils/**`

## Context

- Shared tooling, build extensions, and test utilities require the capability to invoke external processes from within shared utility modules.
- Direct invocation of raw shell command strings creates risks of argument parsing bugs, command injection vulnerabilities, and lost stream outputs.
- Unified command execution wrappers with structured logging and stream capture ensure consistent failure diagnostics across automated workflows.

## Problem Statement

Unstructured and disparate process execution logic across test utilities and documentation tooling leads to unmonitored external executions, inconsistent standard stream handling, and silent failures when external commands return non-zero exit codes.

## Decision

- **R-LM-001** MUST_NOT: Implementations MUST NOT invoke subprocess calls with shell execution flags enabled.

## Policy Block

In scope:

- Subprocess and external process invocation logic within shared tooling and test utility modules.
- Diagnostic command runners requiring standard stream redirection and structured logging.

Out of scope:

- Internal library calls that execute within the runtime without invoking separate operating system processes.
- External services communicating over network protocols.

Exceptions:

- **EXC-20-001** Interactive console operations require direct unbuffered terminal control and user interaction.
  Approval: Architecture review approval
  Documentation: Documented reasoning in the component tracking documentation

## Rationale

- Passing explicit argument lists to subprocess.Popen and subprocess.call eliminates shell-injection vulnerabilities and platform-specific shell quirks.
- Escaping arguments with shlex.quote during logging guarantees accurate reproduction of executed commands during test and build debugging.
- Context-managed execution with explicit stream communication and return code checking ensures deterministic stream collection and fail-fast exception dispatching.

## Consequences

Positive:

- Standardized command invocation provides deterministic stream isolation and detailed diagnostic logs on execution failures.
- Tokenized argument handling prevents shell injection vulnerabilities and parsing inconsistencies across operating environments.
- Uniform exception propagation ensures automated workflows fail fast upon unexpected subprocess errors.

Negative:

- Memory overhead when capturing large standard output or standard error streams synchronously via process communication.
- Increased boilerplate compared to raw system calls when structured error handling and quoting are required.

## Alternatives

- Raw shell string execution via system command wrappers (rejected)
  Rejected because: Exposes command execution to shell injection risks, unreliable argument parsing across platforms, and uncaptured standard error streams.
  When valid: Quick one-off administrative scripting where process isolation and output capture are unnecessary.
- Direct operating system command dispatch without process handles (rejected)
  Rejected because: Fails to capture standard output or standard error streams and lacks fine-grained return code evaluation.
  When valid: Legacy scripts where output redirection and error handling are managed by external operating system pipes.

## Risks

- Buffer exhaustion or blocking when executing external commands that generate massive output streams through standard pipes.
  Mitigation: Direct large output streams to discard devices or temporary file handles instead of unbounded pipe buffers.
  Owner: Engineering Team
- Incomplete command line tokenization when processing complex shell scripts with embedded pipes or redirection operators.
  Mitigation: Decompose complex pipelines into discrete process stages connected via explicit stream pipes.
  Owner: Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Wrap external command execution in context managers to guarantee proper process handle cleanup upon completion or interruption.
- Sanitize and quote all command parameters using shlex.quote when generating informational or debug log statements.
- Ensure non-zero exit codes raise dedicated domain exceptions containing standard output and standard error diagnostics for troubleshooting.

## Continuation Context

Verify commands:

- Discover and run the project test suite using the configured test runner in the repository.
- Discover and execute the repository static analysis and linting checks to verify subprocess usage compliance.

Accept when:

- All unit and integration tests executing external processes pass without unhandled exceptions.
- Static analysis validates that all subprocess invocations use argument sequences without shell execution flags.

## Enforcement

- Verified by: Continuous integration test execution and automated static analysis.
- Verified by: Peer code review for all tooling and utility modifications.
- Violation handling: Pull requests containing unquoted or raw shell subprocess invocations are blocked until refactored to standard executors.
- Violation handling: Continuous integration failures on unexpected return codes require remediation before merging.
- Exception process: Submit an architectural review request detailing why structured subprocess execution is infeasible, accompanied by security sign-off.
