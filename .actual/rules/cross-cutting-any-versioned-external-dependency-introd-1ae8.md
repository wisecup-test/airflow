---
version: 2
name: cross-cutting-any-versioned-external-dependency-introd-1ae8
description: "Standardization on shutil for High-Level Filesystem Management and Executable Discovery: Any Versioned External Dependency Introduced Alongside"
metadata:
  actual:
    adr_id: 1ae8fd82-51ba-49b8-ae58-f670a8a5329e
    status: accepted
    candidate_group_id: b579aca4-4b90-4cf3-b061-968f91cb4f27
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
      - react-plugin-tools/**
      - registry/**
      - send_email.py
---

# Standardization on shutil for High-Level Filesystem Management and Executable Discovery: Any Versioned External Dependency Introduced Alongside

Scope: applies to `dev/breeze/src/airflow_breeze/**`, `dev/react-plugin-tools/**`, `dev/registry/**`, `dev/send_email.py`.

These rules are ALWAYS ACTIVE for:
- Development automation utilities, release validation scripts, and distribution packaging workflows that perform filesystem cleanup or executable lookup.

### Rules

- **R-LM-001** MUST: Any versioned external dependency introduced alongside standard utility modules MUST be verified against the project lock artifact to resolve its exact locked version before implementation.

### Verify

- Discover and run the project static analysis test suite to ensure filesystem operations adhere to approved utility conventions.
- Discover and execute the developer tooling test suite to verify prerequisite resolution and artifact cleanup behavior.

**Accept when:**

- All developer tooling and release verification tests pass with zero unhandled filesystem exceptions.
- Static analysis confirms directory cleanup and binary resolution use standard module interfaces without direct shell deletion calls.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites in continuous integration
- Verified by: Peer code review for pull requests modifying developer commands or release utilities
- Violation handling: Pull requests using platform-specific shell deletion or unapproved subprocess calls for basic filesystem operations are blocked during review.
- Exception process: Submit an exception request detailing container or platform isolation requirements to the tooling maintainers.
</enforcement>
