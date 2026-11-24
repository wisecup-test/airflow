---
version: 2
name: cross-cutting-transient-single-file-operations-use-8910
description: "Adoption of tempfile for Ephemeral Workspace and Artifact Isolation: Transient Single File Operations Use Tempfile"
metadata:
  actual:
    adr_id: 8910e75b-8218-40b6-8a94-699c3e28dd4e
    status: accepted
    candidate_group_id: 04ff5317-8ba3-4547-8f51-3fbf1121ff1c
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Adoption of tempfile for Ephemeral Workspace and Artifact Isolation: Transient Single File Operations Use Tempfile

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- All command implementations and utility modules that create transient files, scratch directories, or staged external downloads

### Rules

- **R-LM-001** MUST: Transient single-file operations MUST use tempfile.NamedTemporaryFile within a context manager or explicit cleanup block to guarantee resource reclamation upon operation completion

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
