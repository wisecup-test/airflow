---
version: 2
name: 9ddaabd2-b2d5-40e8-98ef-b29febd49b42-adoption-of-airflow-breeze-internal-modular-library-architecture-standalone-development-scripts-not-duplicate-core
description: "Adoption of airflow_breeze Internal Modular Library Architecture: Standalone Development Scripts Not Duplicate Core"
metadata:
  actual:
    adr_id: 9ddaabd2-b2d5-40e8-98ef-b29febd49b42
    status: accepted
    candidate_group_id: 0252069d-9c54-4db2-bd65-3bce5c826c9f
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - assign_cherry_picked_prs_with_milestone.py
      - backport/**
      - breeze/src/airflow_breeze/**
      - prune_old_dirs.py
      - react-plugin-tools/**
      - registry/**
      - send_email.py
      - update_github_branch_config.py
      - validate_version_added_fields_in_config.py
      - verify_release_calendar.py
---

# Adoption of airflow_breeze Internal Modular Library Architecture: Standalone Development Scripts Not Duplicate Core

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `assign_cherry_picked_prs_with_milestone.py`
- `backport/**`
- `breeze/src/airflow_breeze/**`
- `prune_old_dirs.py`
- `react-plugin-tools/**`
- `registry/**`
- `send_email.py`
- `update_github_branch_config.py`
- `validate_version_added_fields_in_config.py`
- `verify_release_calendar.py`

## Context

- Development workflows, environment orchestration, and release management operations require consistent interfaces across multiple automation tasks.
- Disparate and uncoordinated development scripts introduce maintenance overhead, inconsistent configuration handling, and fragmented caching mechanisms.
- The codebase establishes an internal workspace library module under the airflow_breeze namespace to consolidate command handling, utility functions, parameter management, and runtime interaction.

## Problem Statement

Repository management and development automation tasks require shared interfaces for user interaction, environment preparation, caching, and release operations. Implementing these procedures as fragmented standalone scripts leads to duplicate logic, inconsistent error handling, and uncoordinated dependency management across development workflows.

## Decision

- **R-LM-001** MUST_NOT: Standalone development scripts MUST_NOT duplicate core environment setup, caching, or release coordination logic outside the designated internal tooling package boundaries.

## Policy Block

In scope:

- All development orchestration modules, release management commands, and utility routines contained within the repository developer tooling package.
- Shared automation helpers responsible for environment setup, configuration caching, and command-line execution interfaces.

Out of scope:

- Production runtime core components that operate outside the developer environment and repository administration boundary.
- Trivial, one-off root repository configuration scripts that do not participate in multi-step environment coordination.

Exceptions:

- **EX-20-001** A developer environment utility requires minimal bootstrap logic before internal modules are initialized or installed.
  Approval: Tooling maintainers review
  Documentation: Document bootstrap constraints and isolation rationale in the module header.

## Rationale

- Consolidating development utilities and commands into the airflow_breeze internal module architecture centralizes maintenance and enforces consistent execution protocols across 48 observed files.
- Standardizing parameter models, cache interaction, and console feedback prevents code drift and reduces defect rates in release automation and local environment management.
- Strict lock-file verification guarantees deterministic execution across disparate local environments and continuous integration runners.

## Consequences

Positive:

- Reusable contracts for caching, console messaging, and command routing across all development tooling.
- Structured package organization facilitates modular expansion and isolated unit testing of automation components.
- Consistent parameter validation and runtime error handling across administrative commands.

Negative:

- Additional architectural overhead required when authoring small development utilities within the formal module structure.
- Tooling contributors must familiarize themselves with the internal module hierarchy and shared contract interfaces.

## Alternatives

- Decentralized Standalone Scripts (rejected)
  Rejected because: Independent scripts lead to code duplication, divergent argument parsing conventions, and uncoordinated cache management.
  When valid: Small repositories with minimal development workflows and no complex environment orchestration.
- Monolithic Single-File Automation Script (rejected)
  Rejected because: A monolithic script hampers modularity, impedes parallel maintenance across teams, and makes targeted unit testing difficult.
  When valid: Repositories with only a single administrative command.

## Risks

- Tightly coupling development automation to a shared internal module may create circular import dependencies across command groups and utility packages.
  Mitigation: Maintain strict layering between command definitions, parameter models, and low-level utility modules within the package architecture.
  Owner: engineering team
- Internal library updates could break existing local developer environments if dependency resolution is not strictly synchronized.
  Mitigation: Enforce lock-file verification and provide self-upgrade validation routines within the tooling package.
  Owner: engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Structure new developer commands by registering them under the appropriate command subpackage group and leveraging shared option definitions.
- Utilize the internal console and caching abstractions for any command output formatting or persisted local state.

## Continuation Context

Verify commands:

- Discover the project test suite runner and execute unit tests covering the internal developer tooling package.
- Discover and run static type checkers and linter suites against the internal tooling package directories.

Accept when:

- All internal developer tooling modules pass repository static analysis and linting checks without violations.
- Unit and integration tests for command routing, parameter models, and cache utilities pass successfully.

## Enforcement

- Verified by: Automated continuous integration checks executing repository test and linting suites.
- Verified by: Pull request code reviews ensuring new developer automation conforms to the internal module structure.
- Violation handling: Pull requests introducing uncoordinated standalone automation scripts or violating internal module boundaries are blocked from merging.
- Exception process: Exceptions require approval from developer tooling maintainers and must include documented justification for external script placement.
