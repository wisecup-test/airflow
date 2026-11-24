---
version: 2
name: cross-cutting-terminal-presentation-logic-respect-envi-68f6
description: "Rich Library Adoption for Terminal Output and Console Formatting: Terminal Presentation Logic Respect Environment Configurations"
metadata:
  actual:
    adr_id: 68f644de-b0c5-487b-8888-c0cb38784454
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

# Rich Library Adoption for Terminal Output and Console Formatting: Terminal Presentation Logic Respect Environment Configurations

Scope: applies to `airflow-ctl/docs/**`, `airflow-ctl/src/airflowctl/ctl/**`.

These rules are ALWAYS ACTIVE for:
- All command-line interface entry points, command execution wrappers, and subcommand modules producing terminal output.
- Diagnostic reporting, credential presentation, and execution failure handling within CLI command workflows.

### Rules

- **R-LM-001** MUST: Terminal presentation logic MUST respect terminal environment configurations and output flags governing color suppression and plain text mode.

### Verify

- Discover and execute the project code analysis and linting scripts to verify compliance with terminal output formatting standards.
- Discover and run the automated test suite covering CLI command execution to verify terminal output and error message rendering.
- Execute command integration test scenarios under simulated headless and non-color environments to verify proper color suppression.

**Accept when:**

- All command-line interface subcommands emit styled messages and diagnostics exclusively through Rich abstractions without unmanaged stream writes.
- Terminal output automatically suppresses styling and formatting markup when color suppression environment variables are asserted or when non-interactive streams are detected.
- Automated test suites and static analysis checks covering CLI command execution and output formatting pass with zero violations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline running static analysis and linter rules prohibiting unapproved standard output writes.
- Verified by: Pull request code reviews by CLI module code owners verifying proper application of Rich console APIs.
- Verified by: Automated unit and integration test suites validating CLI output formatting and exception handling.
- Violation handling: Pull requests containing unapproved standard stream writes, raw ANSI escape codes, or unhandled command exceptions are blocked until refactored to use Rich APIs.
- Violation handling: Existing violations identified during periodic codebase audits must be tracked as technical debt and remediated according to milestone priorities.
- Exception process: Exceptions for raw stream output in machine-readable pipelines must be formally requested via architecture decision proposal and approved by CLI module maintainers.
- Exception process: Approved exceptions must be documented with technical rationale directly adjacent to the non-conforming implementation.
</enforcement>
