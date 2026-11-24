---
version: 2
name: cross-cutting-host-operating-system-hardware-architect-0dd3
description: "Adoption of platform Module for Host Architecture and Runtime Environment Detection: Host Operating System Hardware Architecture Execution"
metadata:
  actual:
    adr_id: 0dd302b6-2194-4a2d-9e20-7e91a627a798
    status: accepted
    candidate_group_id: 098aa190-ed0a-4cde-9bac-dea2f56cb5fd
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
      - ide_setup/**
---

# Adoption of platform Module for Host Architecture and Runtime Environment Detection: Host Operating System Hardware Architecture Execution

Scope: applies to `dev/breeze/src/airflow_breeze/**`, `dev/ide_setup/**`.

These rules are ALWAYS ACTIVE for:
- Subsystems, utilities, and developer commands performing host system detection, hardware architecture normalization, and virtualization environment checks.
- Build, test, and container runtime orchestration scripts requiring platform-specific configuration parameters.

### Rules

- **R-LM-001** MUST: All host operating system, hardware architecture, and execution environment introspection MUST use the platform module encapsulated within dedicated host utility contracts.

### Verify

- Discover the project test runner configuration from repository manifests and execute unit tests covering platform detection and architecture normalization utilities.
- Discover the repository static analysis and linting configuration to verify platform utility imports and architecture enum consistency across all modules.

**Accept when:**

- All unit tests covering host information utilities and platform architecture normalization pass without failure across supported environments.
- Static analysis checks confirm that host operating system and architecture detection logic is isolated to designated platform utility modules.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test suites executing across multiple host operating system runners.
- Verified by: Pull request code reviews ensuring direct platform inspections remain confined to dedicated utility layers.
- Violation handling: Automated build and test suite failure blocking pull request merge.
- Violation handling: Mandatory refactoring of ad-hoc platform checks into centralized platform utility functions.
- Exception process: Submit an architectural review request documenting why standard platform utilities cannot satisfy the host inspection requirement, accompanied by fallback tests.
</enforcement>
