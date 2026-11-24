---
version: 2
name: cross-cutting-unit-test-configurations-not-embed-642e
description: "Vitest Configuration via vitest/config for Unit Testing: Unit Test Configurations Not Embed Environment"
metadata:
  actual:
    adr_id: 642e8e1f-21e2-4353-b9c2-05cbeab73782
    status: accepted
    candidate_group_id: 03e7f149-5ff1-4a59-9734-db3e688743cb
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
      - react-plugin-tools/react_plugin_template/**
---

# Vitest Configuration via vitest/config for Unit Testing: Unit Test Configurations Not Embed Environment

Scope: applies to `dev/breeze/src/airflow_breeze/**`, `dev/react-plugin-tools/react_plugin_template/**`.

These rules are ALWAYS ACTIVE for:
- Plugin template workspaces that declare unit testing configurations using vitest/config

### Rules

- **R-TS-001** MUST_NOT: Unit test configurations MUST NOT embed environment-specific orchestration scripts or execution paths intended for broader repository management boundaries.

### Verify

- Discover the workspace test execution script from the project manifest and execute it to verify unit test configuration.
- Discover the project build verification command from the workspace configuration and execute it to confirm build and type validation succeed.

**Accept when:**

- The workspace test runner executes all unit tests successfully according to the configuration definition.
- The build and configuration validation processes complete with zero exit codes and no configuration syntax errors.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks that execute workspace test suites
- Verified by: Peer code review of configuration files in template workspaces
- Violation handling: Continuous integration job failures blocking pull request merges
- Violation handling: Code review rejection requiring correction to standard test configuration definitions
- Exception process: Submit an architectural exception request documented with rationale to the engineering lead for review
</enforcement>
