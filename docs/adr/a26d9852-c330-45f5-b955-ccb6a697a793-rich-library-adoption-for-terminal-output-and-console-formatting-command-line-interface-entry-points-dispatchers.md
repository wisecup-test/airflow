---
version: 2
name: a26d9852-c330-45f5-b955-ccb6a697a793-rich-library-adoption-for-terminal-output-and-console-formatting-command-line-interface-entry-points-dispatchers
description: "Rich Library Adoption for Terminal Output and Console Formatting: Command Line Interface Entry Points Dispatchers"
metadata:
  actual:
    adr_id: a26d9852-c330-45f5-b955-ccb6a697a793
    status: accepted
    candidate_group_id: 211e2ff1-e746-4aee-9b7e-5080fb2c082b
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - docs/**
      - src/airflowctl/ctl/**
---

# Rich Library Adoption for Terminal Output and Console Formatting: Command Line Interface Entry Points Dispatchers

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `docs/**`
- `src/airflowctl/ctl/**`

## Context

- Command-line interface applications require standardized visual presentation for status messages, interactive prompts, and error diagnostics across multiple execution subcommands.
- Unstructured console output via standard stream writes leads to inconsistent terminal styling, lack of color synchronization, and difficult error parsing across distributed CLI command modules.
- Direct standard stream manipulation complicates support for terminal capabilities such as color profile detection, terminal width wrapping, and adherence to environment variables governing colored output.
- Centralizing console presentation and terminal styling around a dedicated terminal library ensures uniform user experience, consistent error reporting, and robust terminal capability handling across all CLI subcommands.

## Problem Statement

Command-line interfaces across the codebase require a unified mechanism for formatting diagnostic output, displaying colored status banners, and reporting execution failures. In the absence of a standardized terminal presentation library, individual command handlers implement ad-hoc formatting and raw output routines, leading to inconsistent user interfaces, poor error diagnostics, and unreliable handling of terminal display environments.

## Decision

- **R-LM-001** MUST: All command-line interface entry points, command dispatchers, and subcommand handlers MUST standardize on the Rich library for console output formatting, terminal message styling, and user-facing error reporting.

## Policy Block

In scope:

- All command-line interface entry points, command execution wrappers, and subcommand modules producing terminal output.
- Diagnostic reporting, credential presentation, and execution failure handling within CLI command workflows.

Out of scope:

- Machine-readable data pipelines emitting structured formats such as raw JSON or CSV to non-interactive redirected pipes.
- Headless background daemons and service processes writing exclusively to centralized log aggregators.

Exceptions:

- **EXC-20-001** Standard input or output is redirected to an automated non-interactive stream requiring purely raw serialized data without ANSI tags or markup.
  Approval: Architecture review approval and code owner sign-off.
  Documentation: Documented inline at the stream redirection check and recorded in the module architecture notes.

## Rationale

- Adopting the Rich library centralizes terminal presentation logic, ensuring that color styling, semantic tags, and formatting rules remain consistent across all CLI commands.
- Rich provides native awareness of terminal capabilities and automatically suppresses color and styling when running in headless environments or when standard color suppression environment variables are asserted.
- Using `rich.print` with markup eliminates fragile manual ANSI code construction and reduces code complexity across command implementation handlers.
- Centralized error formatting with Rich improves developer experience and user troubleshooting by rendering clean, structured error summaries during command failures.

## Consequences

Positive:

- Establishes a uniform visual hierarchy, consistent color tagging, and standardized typography across all command-line interface subcommands.
- Provides automatic terminal capability detection and seamless adherence to environment variables governing color suppression.
- Eliminates custom ANSI escape code formatting and fragmented string concatenation across command handlers.
- Improves diagnostic clarity through structured console error rendering during execution failures.

Negative:

- Introduces an external dependency that must be maintained, locked, and audited within the project repository.
- Requires command developers to learn Rich markup conventions and console abstractions rather than relying on standard stream output functions.
- Carries a risk of malformed output if developers misapply markup tags or circumvent centralized console abstractions.

## Alternatives

- Direct standard stream output using raw ANSI escape codes (rejected)
  Rejected because: Manual ANSI escape sequences lead to widespread code duplication, fragile string formatting, and failure to honor color suppression controls across varied terminal environments.
  When valid: Single-file utility scripts with zero tolerance for external dependencies.
- Relying exclusively on standard library logging for terminal output (rejected)
  Rejected because: Standard library logging lacks high-fidelity markup, flexible color styling, and formatted status banners necessary for user-facing interactive command-line tools.
  When valid: Background services and headless daemons writing strictly to centralized log collectors.

## Risks

- Non-interactive automated scripts consuming CLI output may fail if terminal markup or color sequences leak into stdout.
  Mitigation: Ensure all commands respect color suppression environment variables and support raw output modes for redirected streams.
  Owner: CLI Engineering Team
- Inconsistent usage of Rich across different subcommands if developers mix standard print routines with Rich markup.
  Mitigation: Enforce use of Rich console methods via static code analysis, automated linter rules, and peer review checks.
  Owner: CLI Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Developers must encapsulate terminal message emissions using Rich console functions and semantic markup tags, avoiding manual string formatting for colorization.
- Error boundaries in command dispatchers must wrap execution calls in safe handling blocks that format exceptions using Rich error rendering before exiting.

## Continuation Context

Verify commands:

- Discover and execute the project code analysis and linting scripts to verify compliance with terminal output formatting standards.
- Discover and run the automated test suite covering CLI command execution to verify terminal output and error message rendering.
- Execute command integration test scenarios under simulated headless and non-color environments to verify proper color suppression.

Accept when:

- All command-line interface subcommands emit styled messages and diagnostics exclusively through Rich abstractions without unmanaged stream writes.
- Terminal output automatically suppresses styling and formatting markup when color suppression environment variables are asserted or when non-interactive streams are detected.
- Automated test suites and static analysis checks covering CLI command execution and output formatting pass with zero violations.

## Enforcement

- Verified by: Automated continuous integration pipeline running static analysis and linter rules prohibiting unapproved standard output writes.
- Verified by: Pull request code reviews by CLI module code owners verifying proper application of Rich console APIs.
- Verified by: Automated unit and integration test suites validating CLI output formatting and exception handling.
- Violation handling: Pull requests containing unapproved standard stream writes, raw ANSI escape codes, or unhandled command exceptions are blocked until refactored to use Rich APIs.
- Violation handling: Existing violations identified during periodic codebase audits must be tracked as technical debt and remediated according to milestone priorities.
- Exception process: Exceptions for raw stream output in machine-readable pipelines must be formally requested via architecture decision proposal and approved by CLI module maintainers.
- Exception process: Approved exceptions must be documented with technical rationale directly adjacent to the non-conforming implementation.
