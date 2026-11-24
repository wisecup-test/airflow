---
version: 2
name: cross-cutting-command-handlers-define-execution-logic-dd52
description: "Adoption of github.com/spf13/cobra for Command-Line Interface Definition: Command Handlers Define Execution Logic Error"
metadata:
  actual:
    adr_id: dd52a6ff-9d15-4116-8701-aa06653799f1
    status: accepted
    candidate_group_id: 3f6d8eed-96da-4b76-a75d-637735f622ff
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - cmd/airflow-go-pack/**
      - edge/commands/**
---

# Adoption of github.com/spf13/cobra for Command-Line Interface Definition: Command Handlers Define Execution Logic Error

Scope: applies to `go-sdk/cmd/airflow-go-pack/**`, `go-sdk/edge/commands/**`.

These rules are ALWAYS ACTIVE for:
- All command-line interface entrypoints, root commands, and subcommands in the repository
- Operational tooling requiring argument parsing, command hierarchy, and execution dispatch

### Rules

- **R-LM-001** MUST: Command handlers MUST define execution logic using error-returning run functions to propagate execution failures to the runtime harness.

### Verify

- # Discover repository dependency manifest and confirm resolved lockfile matches active environment
- # Discover and run repository static analysis to verify all command packages conform to framework conventions
- # Discover and run repository test suites validating CLI command dispatch and flag parsing

**Accept when:**

- All command-line interface entrypoints and subcommands route argument handling and execution exclusively through the adopted framework.
- All command handlers propagate execution errors to the runtime harness without unhandled panics.
- Repository test and static analysis verification suites pass across all command modules.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Continuous integration pipelines executing repository test and static analysis suites
- Verified by: Peer code reviews validating adherence to command structure conventions and separation of concerns
- Violation handling: Pull requests introducing custom argument parsers or alternative command libraries are blocked until aligned with the standard.
- Violation handling: Existing non-conforming command definitions are flagged as architectural debt and scheduled for refactoring.
- Exception process: Exceptions require formal architectural review and consensus from repository maintainers before merging.
- Exception process: Approved exceptions must record technical justification and scope limitations in repository documentation.
</enforcement>
