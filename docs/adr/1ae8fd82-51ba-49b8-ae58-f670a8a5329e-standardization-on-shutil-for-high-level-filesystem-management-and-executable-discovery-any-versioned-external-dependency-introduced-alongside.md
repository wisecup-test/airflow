---
version: 2
name: 1ae8fd82-51ba-49b8-ae58-f670a8a5329e-standardization-on-shutil-for-high-level-filesystem-management-and-executable-discovery-any-versioned-external-dependency-introduced-alongside
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

Date: 2025-05-14
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/**`
- `react-plugin-tools/**`
- `registry/**`
- `send_email.py`

## Context

- Developer environment tooling and release management workflows require directory cleanup, artifact extraction, and prerequisite executable resolution.
- Operating system differences create inconsistencies when invoking shell-specific filesystem commands across execution environments.
- Automation scripts across developer commands, provider distributions, and release validators routinely import and invoke high-level filesystem utilities from the standard runtime.

## Problem Statement

Automation and release verification workflows require consistent, cross-platform mechanisms for directory tree removal, distribution staging, and environment prerequisite checks without introducing platform-dependent shell command variations or unmanaged external dependencies.

## Decision

- **R-LM-001** MUST: Any versioned external dependency introduced alongside standard utility modules MUST be verified against the project lock artifact to resolve its exact locked version before implementation.

## Policy Block

In scope:

- Development automation utilities, release validation scripts, and distribution packaging workflows that perform filesystem cleanup or executable lookup.

Out of scope:

- Core runtime workflow execution paths that do not manage local developer environment files or prerequisite binaries.

Exceptions:

- **EXC-20-001** A specialized build container or platform-specific isolation driver manages filesystem lifecycle externally.
  Approval: Tooling maintainers
  Documentation: Document container isolation lifecycle in the module pull request.

## Rationale

- Using the shutil module provides portable filesystem operations that behave consistently across heterogeneous developer operating systems.
- Relying on standard runtime capabilities eliminates unnecessary external dependencies for routine file tree and binary discovery tasks.

## Consequences

Positive:

- Directory cleanup and binary prerequisite checks operate portably without shell dialect discrepancies.
- Tooling scripts avoid external packaging dependencies for basic filesystem manipulation.

Negative:

- High-level directory removal operations execute synchronously and permanently without intermediate recovery mechanisms.
- Complex filesystem permissions or symbolic link hierarchies require additional defensive error handling.

## Alternatives

- Subprocess shell command execution for directory removal and binary checks (rejected)
  Rejected because: Spawning shell commands introduces cross-platform portability failures and platform-dependent exit status interpretation.
  When valid: When operating in locked execution environments where shell built-ins are strictly mandated.
- Third-party filesystem management library adoption (rejected)
  Rejected because: Adding external dependencies for directory cleanup and binary lookup increases supply-chain maintenance overhead without architectural benefit.
  When valid: When advanced transactional filesystem rollback capabilities are required.

## Risks

- Unchecked recursive directory deletion could delete unintended files if path resolution logic receives malformed inputs.
  Mitigation: Validate target path existence and enforce strict path resolution boundaries prior to recursive deletion.
  Owner: Engineering team
- Executable discovery may locate incompatible binary variants if system environment search paths are polluted.
  Mitigation: Inspect discovered executable version output following resolution before proceeding with execution.
  Owner: Engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Use shutil.rmtree with appropriate error handling when purging build artifacts and distribution directories.
- Resolve prerequisite binary availability using shutil.which before triggering external validation subroutines.

## Continuation Context

Verify commands:

- Discover and run the project static analysis test suite to ensure filesystem operations adhere to approved utility conventions.
- Discover and execute the developer tooling test suite to verify prerequisite resolution and artifact cleanup behavior.

Accept when:

- All developer tooling and release verification tests pass with zero unhandled filesystem exceptions.
- Static analysis confirms directory cleanup and binary resolution use standard module interfaces without direct shell deletion calls.

## Enforcement

- Verified by: Automated test suites in continuous integration
- Verified by: Peer code review for pull requests modifying developer commands or release utilities
- Violation handling: Pull requests using platform-specific shell deletion or unapproved subprocess calls for basic filesystem operations are blocked during review.
- Exception process: Submit an exception request detailing container or platform isolation requirements to the tooling maintainers.
