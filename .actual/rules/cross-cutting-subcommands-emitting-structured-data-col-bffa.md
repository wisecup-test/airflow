---
version: 2
name: cross-cutting-subcommands-emitting-structured-data-col-bffa
description: "Rich Console Subclassing via AirflowConsole for CLI Output Protocols: Subcommands Emitting Structured Data Collections Invoke"
metadata:
  actual:
    adr_id: bffa977f-87ea-4a31-a203-f7521a2813b8
    status: accepted
    candidate_group_id: 40a1c0b8-dccb-4b31-8107-a44b5e4e5462
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - docs/**
      - src/airflowctl/**
---

# Rich Console Subclassing via AirflowConsole for CLI Output Protocols: Subcommands Emitting Structured Data Collections Invoke

Scope: applies to `airflow-ctl/docs/**`, `airflow-ctl/src/airflowctl/**`.

These rules are ALWAYS ACTIVE for:
- All command-line subcommands emitting informational or structured data responses to stdout or stderr.
- Terminal formatting and presentation utility modules within the command-line interface layer.

### Rules

- **R-PEA-001** MUST: Subcommands emitting structured data collections MUST invoke the AirflowConsole print_as dispatch method, supplying data payloads along with the requested format identifier and optional mapping transformations.

### Verify

- Discover and execute the project unit and integration test suite to verify console output formatting and command rendering protocols.
- Discover and execute the project linter and static type analysis scripts to validate compliance with console subclassing signatures.

**Accept when:**

- All automated test suites for command-line output formatting pass without regression.
- Static typing checks verify that all commands outputting structured collections interact through the centralized console abstraction.
- Output redirection in non-interactive shell streams maintains consistent column alignment and valid syntax without stream corruption.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks scanning command implementations for direct standard output invocations.
- Verified by: Comprehensive test suites verifying multi-format rendering across all CLI subcommands.
- Verified by: Peer code review for all pull requests adding or modifying command presentation layers.
- Violation handling: Pull requests containing unapproved direct print statements or bypassing the console protocol will be rejected by continuous integration gates.
- Violation handling: Violations discovered in the codebase must be refactored to use the centralized console abstraction before release.
- Exception process: Submit an architectural exception request detailing the technical necessity for bypassing the console protocol to the Architecture Review Board.
- Exception process: Approved exceptions must be tracked with an expiration date and recorded in the architectural documentation repository.
</enforcement>
