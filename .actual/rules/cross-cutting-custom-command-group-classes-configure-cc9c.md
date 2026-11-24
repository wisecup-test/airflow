---
version: 2
name: cross-cutting-custom-command-group-classes-configure-cc9c
description: "rich_click Custom Command Lifecycle and Output Capture Integration: Custom Command Group Classes Configure Their"
metadata:
  actual:
    adr_id: cc9ccadf-9092-4354-bbbb-5a3999f96cac
    status: accepted
    candidate_group_id: 481381f5-0a91-4ff6-9905-f5278118e88e
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/utils/**
---

# rich_click Custom Command Lifecycle and Output Capture Integration: Custom Command Group Classes Configure Their

Scope: applies to `dev/breeze/src/airflow_breeze/utils/**`.

These rules are ALWAYS ACTIVE for:
- Development CLI command definitions and group registries.
- Terminal help text formatting and documentation recording utilities.

### Rules

- **R-LM-001** SHOULD: Custom command group classes SHOULD configure their command_class attribute to the custom command class to automatically cascade execution hooks to registered subcommands.

### Verify

- Discover and execute the project CLI test suite to verify that custom command lifecycle and reproduction hooks execute cleanly.
- Discover and run the terminal output recording verification routine to ensure output generation produces valid artifacts.

**Accept when:**

- All custom CLI commands and groups successfully invoke their delegated actions while executing lifecycle reproduction hooks.
- Output recording cleanly captures formatted console content without altering standard command exit codes.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites validating custom command invocation and error handling.
- Verified by: Code review checks ensuring new CLI commands inherit from custom command groups rather than standard base primitives.
- Violation handling: Pull requests introducing direct standard click command instantiations or bypassing group abstractions will be blocked until refactored.
- Exception process: Exceptions require approval from the tooling maintainers with documented justification explaining why custom command lifecycle interception cannot be used.
</enforcement>
