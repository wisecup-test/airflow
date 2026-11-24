---
version: 2
name: cross-cutting-automation-commands-routines-not-write-07a3
description: "Adoption of tempfile for Ephemeral Workspace and Artifact Isolation: Automation Commands Routines Not Write Intermediate"
metadata:
  actual:
    adr_id: 07a3b072-61c8-448a-82f4-28e90e3c1309
    status: accepted
    candidate_group_id: 04ff5317-8ba3-4547-8f51-3fbf1121ff1c
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Adoption of tempfile for Ephemeral Workspace and Artifact Isolation: Automation Commands Routines Not Write Intermediate

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- All command implementations and utility modules that create transient files, scratch directories, or staged external downloads

### Rules

- **R-LM-001** MUST_NOT: Automation commands and routines MUST_NOT write intermediate runtime artifacts or downloaded external assets directly to fixed repository paths

### Verify

- echo 'Discover and run project test suite via configured build tool'
- echo 'Discover and execute project linter and static analysis checks'

**Accept when:**

- All discovered project test suites execute successfully with zero failures
- All static analysis and linting checks report zero rule violations across modified modules
- No temporary directories or orphan files remain in repository paths after test suite completion

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline execution
- Verified by: Peer code review verification against temporary filesystem usage rules
- Violation handling: Continuous integration failure and rejection of pull requests introducing hardcoded scratch paths
- Violation handling: Code review comments requesting refactoring to standard tempfile primitives
- Exception process: Submission of an architectural review request documenting technical necessity for static scratch space
</enforcement>
