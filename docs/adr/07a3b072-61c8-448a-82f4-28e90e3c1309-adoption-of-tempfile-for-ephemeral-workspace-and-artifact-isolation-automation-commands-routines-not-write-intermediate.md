---
version: 2
name: 07a3b072-61c8-448a-82f4-28e90e3c1309-adoption-of-tempfile-for-ephemeral-workspace-and-artifact-isolation-automation-commands-routines-not-write-intermediate
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

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/**`

## Context

- Tooling commands and automation utilities frequently generate intermediate files, stage downloaded assets, and assemble runtime configuration files during execution
- Writing intermediate outputs to static workspace locations introduces concurrency hazards, cross-process race conditions, and repository tree contamination
- Standardizing ephemeral file operations on dedicated tempfile primitives provides process-isolated scratch space managed across host operating environments

## Problem Statement

Automation utilities and command scripts risk corrupting shared repository state and failing during concurrent execution when staging temporary assets in fixed filesystem locations without proper isolation and automatic lifecycle cleanup.

## Decision

- **R-LM-001** MUST_NOT: Automation commands and routines MUST_NOT write intermediate runtime artifacts or downloaded external assets directly to fixed repository paths

## Policy Block

In scope:

- All command implementations and utility modules that create transient files, scratch directories, or staged external downloads

Out of scope:

- Persistent data storage, checked-in repository assets, and long-lived system configurations

## Rationale

- Evidence demonstrates widespread adoption of tempfile primitives across utility scripts to prevent state pollution across distinct execution contexts
- Utilizing operating system temporary file abstractions ensures portability across varied developer environments while isolating parallel executions
- Context-managed temporary primitives minimize resource leaks by automatically binding disk resource lifetimes to operation scopes

## Consequences

Positive:

- Eliminates cross-process collisions when multiple automation workflows execute in parallel
- Keeps repository working trees clean of intermediate build and download artifacts
- Provides consistent cross-platform temporary file allocation and permissions handling

Negative:

- Requires diligent resource tracking to prevent dangling directories when using unmanaged creation functions
- Introduces minor filesystem latency when allocating and cleaning up extensive ephemeral directory hierarchies

## Alternatives

- Direct creation of temporary files in hardcoded workspace subdirectories (rejected)
  Rejected because: Causes file collisions during concurrent runs, pollutes source control status, and risks leaking stale artifacts across executions
  When valid: When inspecting intermediate assets during dedicated interactive local debugging sessions
- In-memory buffer abstractions without physical disk persistence (rejected)
  Rejected because: Incompatible with external CLI utilities and child processes that require standard filesystem paths as input arguments
  When valid: When data operations are purely internal to the calling process and do not interface with filesystem-bound tooling

## Risks

- Failure to clean up ephemeral directories created without context management leading to disk space exhaustion over time
  Mitigation: Encourage context manager patterns and wrap unmanaged directory allocations in cleanup blocks or process exit hooks
  Owner: engineering team
- Temporary filesystem storage exhaustion when operating on exceptionally large artifacts or multi-gigabyte staging layers
  Mitigation: Validate available temporary disk capacity and permit overriding the base temporary directory location through standard environment configuration
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
- Ensure all temporary directory allocations specify clear prefixes reflecting the calling component to facilitate debugging and resource tracking
- Prefer context-managed resource wrappers over bare creation routines to guarantee cleanup even when unhandled exceptions occur

## Continuation Context

Verify commands:

- echo 'Discover and run project test suite via configured build tool'
- echo 'Discover and execute project linter and static analysis checks'

Accept when:

- All discovered project test suites execute successfully with zero failures
- All static analysis and linting checks report zero rule violations across modified modules
- No temporary directories or orphan files remain in repository paths after test suite completion

## Enforcement

- Verified by: Automated continuous integration pipeline execution
- Verified by: Peer code review verification against temporary filesystem usage rules
- Violation handling: Continuous integration failure and rejection of pull requests introducing hardcoded scratch paths
- Violation handling: Code review comments requesting refactoring to standard tempfile primitives
- Exception process: Submission of an architectural review request documenting technical necessity for static scratch space
