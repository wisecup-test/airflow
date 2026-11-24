---
version: 2
name: 30131ac4-85e1-4e7b-a931-ba2735b3a1dc-adoption-of-platform-module-for-host-architecture-and-runtime-environment-detection-application-commands-workflow-routines-not-invoke
description: "Adoption of platform Module for Host Architecture and Runtime Environment Detection: Application Commands Workflow Routines Not Invoke"
metadata:
  actual:
    adr_id: 30131ac4-85e1-4e7b-a931-ba2735b3a1dc
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

# Adoption of platform Module for Host Architecture and Runtime Environment Detection: Application Commands Workflow Routines Not Invoke

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/**`
- `ide_setup/**`

## Context

- Developer orchestration scripts and continuous integration workflows must operate consistently across heterogeneous host operating systems and CPU architectures.
- Scattered and uncoordinated operating system and CPU architecture inspections cause brittle execution branching, duplicate machine translation mappings, and inconsistent environment assumptions.
- Standardizing host environment discovery and hardware architecture normalization through the platform module establishes a single authoritative source of truth for runtime adaptation.

## Problem Statement

Developer tools, orchestration scripts, and continuous integration workflows need to adapt behavior based on host operating systems, hardware architectures, and virtualization environments. Without a standardized introspection mechanism, components risk implementing divergent host-detection heuristics, duplicating machine-to-architecture translation logic, and executing unportable shell commands that fail across diverse developer workstations and continuous integration runners.

## Decision

- **R-LM-001** MUST_NOT: Application commands and workflow routines MUST NOT invoke raw shell commands or parse raw operating system files directly when the equivalent capability exists in the platform module.

## Policy Block

In scope:

- Subsystems, utilities, and developer commands performing host system detection, hardware architecture normalization, and virtualization environment checks.
- Build, test, and container runtime orchestration scripts requiring platform-specific configuration parameters.

Out of scope:

- Target container payloads and guest environments running under predefined static platform specifications where host discovery is not required.
- Pure domain logic components that operate independently of host operating systems and hardware architectures.

Exceptions:

- **EXP-20-001** A virtualization or container boundary provides specialized pseudo-filesystem entries that are not exposed through standard library platform module interfaces.
  Approval: Architecture review and maintainer approval.
  Documentation: Document the limitation and platform-specific fallback behavior in the corresponding utility module contract.

## Rationale

- Centralizing platform introspection around the platform module provides stable, cross-platform APIs that avoid unportable external shell commands and ad-hoc filesystem inspections.
- Mapping raw platform strings from platform into centralized enumerated types and lookup structures prevents fragmentation of architecture identifiers across commands and utilities.
- Standardized environment validation enables early failure and actionable diagnostic messages when an unsupported host or virtualization configuration is detected.

## Consequences

Positive:

- Provides uniform host operating system and hardware architecture representation across all tooling and orchestration commands.
- Eliminates fragile shell-out invocations and direct filesystem parsing for system architecture queries.
- Centralizes architecture translation mappings, simplifying maintenance when supporting new CPU architectures or virtual environments.

Negative:

- Platform utility abstractions must accommodate subtle variations in machine string formatting across host operating systems.
- Certain virtualized kernel environments require supplemental heuristic checks beyond standard library introspection queries.

## Alternatives

- Execute external shell utilities and parse standard output for platform and architecture detection (rejected)
  Rejected because: Spawning external subprocesses introduces execution overhead, unportable command dependencies, and fragile text parsing that breaks across non-standard shells.
  When valid: When operating in restricted execution environments where standard runtime introspection modules are unavailable.
- Adopt an external third-party hardware inspection library dependency (rejected)
  Rejected because: Introducing third-party dependencies increases maintenance surface and supply-chain exposure when core standard modules satisfy architecture identification requirements.
  When valid: When deep hardware telemetry, thermal reporting, or detailed device sensor metrics are explicitly required.

## Risks

- Virtualized host environments or custom kernel distributions may return unexpected release or architecture strings from platform inspection APIs.
  Mitigation: Encapsulate platform calls behind fallback-tolerant normalization helpers that log diagnostic details when unrecognized identifiers appear.
  Owner: Engineering Team
- Downstream consumers might bypass centralized platform utilities and directly import platform APIs with divergent mapping logic.
  Mitigation: Enforce import boundaries and architectural layering through automated linters and continuous integration review.
  Owner: Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Centralize all calls to platform module functions within dedicated host utility contracts rather than invoking them directly inside command handlers.
- Ensure architecture translation mappings from platform machine inspection normalize strings to lowercase before checking conversion mappings and assigning architecture enumerations.

## Continuation Context

Verify commands:

- Discover the project test runner configuration from repository manifests and execute unit tests covering platform detection and architecture normalization utilities.
- Discover the repository static analysis and linting configuration to verify platform utility imports and architecture enum consistency across all modules.

Accept when:

- All unit tests covering host information utilities and platform architecture normalization pass without failure across supported environments.
- Static analysis checks confirm that host operating system and architecture detection logic is isolated to designated platform utility modules.

## Enforcement

- Verified by: Automated continuous integration test suites executing across multiple host operating system runners.
- Verified by: Pull request code reviews ensuring direct platform inspections remain confined to dedicated utility layers.
- Violation handling: Automated build and test suite failure blocking pull request merge.
- Violation handling: Mandatory refactoring of ad-hoc platform checks into centralized platform utility functions.
- Exception process: Submit an architectural review request documenting why standard platform utilities cannot satisfy the host inspection requirement, accompanied by fallback tests.
