---
version: 2
name: cross-cutting-scripts-define-explicit-parameter-types-b8ce
description: "argparse Module Adoption for Command-Line Script Interfaces: Scripts Define Explicit Parameter Types Help"
metadata:
  actual:
    adr_id: b8ce1786-725c-43bb-8db1-deac33cf6000
    status: accepted
    candidate_group_id: 6082a516-84bd-4866-a226-b6d59946d385
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - ide_setup/**
      - prune_old_dirs.py
      - react-plugin-tools/**
      - registry/**
      - verify_release_calendar.py
---

# argparse Module Adoption for Command-Line Script Interfaces: Scripts Define Explicit Parameter Types Help

Scope: applies to `dev/ide_setup/**`, `dev/prune_old_dirs.py`, `dev/react-plugin-tools/**`, `dev/registry/**`, `dev/verify_release_calendar.py`.

These rules are ALWAYS ACTIVE for:
- Standalone operational, maintenance, and automation scripts executed from command-line environments.
- Data extraction, registry synchronization, environment setup, and verification utilities within repository tooling.

### Rules

- **R-LM-001** MUST: Scripts MUST define explicit parameter types, help descriptions, and default values within the argument parser configuration rather than manually inspecting raw argument arrays.

### Verify

- Discover and run the project static analysis suite to verify that command-line scripts import argparse and adhere to parser configuration conventions.
- Discover and execute the automated test runner targeting developer tooling and script test suites to validate argument parsing edge cases.

**Accept when:**

- All operational and automation scripts parse command-line options using argparse with structured help documentation and exit codes.
- Repository static analysis checks and test validation suites pass with zero violations regarding unparsed argument access.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks integrated into repository continuous integration pipelines.
- Verified by: Peer code review during change evaluation for developer tooling and operational automation scripts.
- Violation handling: Changes introducing ad-hoc argument vector inspection or unapproved external CLI dependencies will be blocked during review until refactored to use argparse.
- Exception process: Exceptions require submission of an architectural review request detailing specific technical requirements that cannot be satisfied by the standard module.
</enforcement>
