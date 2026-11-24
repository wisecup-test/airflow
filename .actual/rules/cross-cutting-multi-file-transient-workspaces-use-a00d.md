---
version: 2
name: cross-cutting-multi-file-transient-workspaces-use-a00d
description: "Adoption of tempfile for Ephemeral Workspace and Artifact Isolation: Multi File Transient Workspaces Use Tempfile"
metadata:
  actual:
    adr_id: a00d6f73-4b07-4b68-857b-a69881839430
    status: accepted
    candidate_group_id: 04ff5317-8ba3-4547-8f51-3fbf1121ff1c
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Adoption of tempfile for Ephemeral Workspace and Artifact Isolation: Multi File Transient Workspaces Use Tempfile

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- All command implementations and utility modules that create transient files, scratch directories, or staged external downloads

### Rules

- **R-LM-001** SHOULD: Multi-file transient workspaces SHOULD use tempfile.TemporaryDirectory or tempfile.mkdtemp with a descriptive prefix to isolate concurrent tasks and avoid namespace collisions

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
