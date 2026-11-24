---
version: 2
name: cross-cutting-before-implementing-code-that-relies-0aab
description: "logging Module for Build and Documentation Infrastructure: Before Implementing Code That Relies Versioned"
metadata:
  actual:
    adr_id: 0aab94db-b8e9-4ce3-ae25-c3457e6908db
    status: accepted
    candidate_group_id: 6862e8b4-cfac-467d-876c-4200115ad465
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - docs/**
      - hatch_build.py
---

# logging Module for Build and Documentation Infrastructure: Before Implementing Code That Relies Versioned

Scope: applies to `airflow-core/docs/**`, `airflow-core/hatch_build.py`.

These rules are ALWAYS ACTIVE for:
- Build hook scripts, packaging automation, and documentation generation configuration modules.
- Lifecycle extensions and compilation hooks executing as part of the project infrastructure.

### Rules

- **R-LM-001** MUST: Before implementing code that relies on versioned external dependencies, the consumer MUST inspect the dependency manifest, identify the build tool, and verify the exact resolved version within the authoritative lock or resolution artifact.

### Verify

- Discover the repository static analysis and linting configuration and execute the prescribed verification command to detect unapproved print statements in build and documentation modules.
- Discover the repository test runner from the project manifest and execute the test suite validating build hook behavior and logging emission.

**Accept when:**

- Static verification confirms that all build hooks and documentation configuration modules emit diagnostic messages using logging logger instances.
- No direct calls to standard output printing exist in build and documentation source modules.
- Automated tests for build hooks execute and demonstrate that log level thresholds respond accurately to runtime configuration.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis and linting rules executed in continuous integration.
- Verified by: Peer code review for changes to build hooks and documentation configuration modules.
- Violation handling: Pull requests containing unformatted standard output calls in build or documentation modules are blocked until migrated to the logging library.
- Exception process: Exceptions require documented justification and approval from platform infrastructure maintainers.
</enforcement>
