---
version: 2
name: cross-cutting-subcommand-parsers-requiring-custom-help-e1a9
description: "argparse CLI Command Definition and Subparser Architecture: Subcommand Parsers Requiring Custom Help Formatting"
metadata:
  actual:
    adr_id: e1a95bda-c3eb-45d9-8d4b-a0409e2a6f7f
    status: accepted
    candidate_group_id: 78f62221-87d2-4bee-9125-a90fc25fc3ac
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/ctl/**
---

# argparse CLI Command Definition and Subparser Architecture: Subcommand Parsers Requiring Custom Help Formatting

Scope: applies to `airflow-ctl/src/airflowctl/ctl/**`.

These rules are ALWAYS ACTIVE for:
- Command-line interface parser construction and argument configuration.
- Subcommand registration and dispatch routing within CLI modules.
- Custom help message formatters and argument resolution proxies.

### Rules

- **R-LM-001** MUST: Subcommand parsers requiring custom help formatting MUST subclass parser formatter classes and override subaction iteration to separate command groups from individual subcommands.

### Verify

- Discover and execute the project test runner script targeting CLI parser configuration and command routing test suites.
- Discover and execute the repository static analysis and formatting check script to verify parser interface compliance.
- Discover and execute the CLI startup latency verification script to ensure deferred command loading operates within expected bounds.

**Accept when:**

- All CLI parser configuration test suites pass without errors or deprecation warnings.
- Parser construction completes without triggering imports of domain execution logic.
- Help formatters display command groups and subcommands in separate formatted sections.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks executing parser test suites.
- Verified by: Peer code review for any changes adding or modifying CLI commands and parser configurations.
- Verified by: Import timing profiling to detect accidental eager dependency loading during parser construction.
- Violation handling: Pull requests introducing eager command imports or improper subparser configurations will be blocked from merging.
- Violation handling: Violations identified post-merge must be remediated in an immediate follow-up patch.
- Exception process: Exceptions must be requested via an architectural review ticket describing the specific command requirements and latency impact.
- Exception process: Approval requires review and sign-off by the CLI subsystem maintainers.
</enforcement>
