---
version: 2
name: cross-cutting-command-implementations-not-embed-enviro-2c92
description: "rich_click Custom Command Lifecycle and Output Capture Integration: Command Implementations Not Embed Environment Variable"
metadata:
  actual:
    adr_id: 2c9202b6-4a52-4966-a414-511932cd8f60
    status: accepted
    candidate_group_id: 481381f5-0a91-4ff6-9905-f5278118e88e
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/utils/**
---

# rich_click Custom Command Lifecycle and Output Capture Integration: Command Implementations Not Embed Environment Variable

Scope: applies to `dev/breeze/src/airflow_breeze/utils/**`.

These rules are ALWAYS ACTIVE for:
- Development CLI command definitions and group registries.
- Terminal help text formatting and documentation recording utilities.

### Rules

- **R-LM-001** MUST_NOT: Command implementations MUST NOT embed environment variable evaluation or output recording logic directly inside command business logic.

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
