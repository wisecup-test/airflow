---
version: 2
name: 5d93fcd8-94d2-4728-be71-58bb16abbe28-adoption-of-shutil-for-high-level-filesystem-and-directory-management-when-relocating-directory-hierarchies-archived-execution
description: "Adoption of shutil for High-Level Filesystem and Directory Management: When Relocating Directory Hierarchies Archived Execution"
metadata:
  actual:
    adr_id: 5d93fcd8-94d2-4728-be71-58bb16abbe28
    status: accepted
    candidate_group_id: 1df8a3a7-4e7d-4f55-ba1f-7f3faa35334b
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# Adoption of shutil for High-Level Filesystem and Directory Management: When Relocating Directory Hierarchies Archived Execution

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/**`
- `src/tests_common/test_utils/**`

## Context

- Build automation, test execution harnesses, and documentation pipelines require creating, relocating, and purging directory structures and operational output artifacts.
- Direct invocation of operating system shell utilities creates platform-dependent execution paths and makes failure modes difficult to trap programmatically across heterogeneous environments.
- Custom recursive traversal and file deletion logic implemented using basic file descriptor primitives introduces boilerplate code and inconsistent error recovery.
- The codebase standardizes high-level directory tree operations using the shutil module to ensure cross-platform consistency and clean artifact isolation.

## Problem Statement

Development scripts and test harnesses across the codebase need to perform composite directory manipulations such as purging test logs, archiving prior execution records, and preparing documentation build output trees. Without a unified module convention for high-level filesystem operations, implementations risk relying on platform-specific shell subprocesses or hand-rolled recursive traversal logic that fails silently or behaves inconsistently across operating environments.

## Decision

- **R-LM-001** MUST: When relocating directory hierarchies or archived execution logs, developers MUST invoke shutil.move rather than combining separate read and write file operations.

## Policy Block

In scope:

- Development tooling, documentation generation scripts, and test infrastructure modules performing filesystem directory creation, relocation, and deletion.
- Lifecycle cleanup fixtures and artifact archiving routines in test harnesses.

Out of scope:

- Low-level binary streaming, high-throughput network transport, or operational runtime database data storage management.
- Core application business logic executing outside build automation, documentation compilation, and test utilities.

## Rationale

- Evidence across documentation builders and test utility classes demonstrates consistent adoption of shutil for directory cleanup and artifact archival.
- Using shutil provides platform-neutral filesystem abstractions that operate reliably across differing host operating systems without invoking shell subprocesses.
- Standardizing on established APIs such as shutil.rmtree and shutil.move reduces boilerplate code and prevents defect-prone manual recursive deletion implementations.

## Consequences

Positive:

- Eliminates platform-specific shell subprocess calls, providing consistent filesystem manipulation behavior across operating systems.
- Simplifies directory tree lifecycle cleanup and artifact isolation in test harnesses and build scripts.
- Ensures unified exception handling and deterministic error management during directory cleanup routines.

Negative:

- High-level directory manipulation functions can suppress unexpected filesystem permissions errors if error-ignoring flags are applied without diagnostic logging.
- Requires coordinating path resolution and target directory existence checks before triggering recursive relocations.

## Alternatives

- Invoke platform shell commands via subprocess execution (rejected)
  Rejected because: Shell commands introduce host operating system discrepancies, platform divergence, and increased execution fragility.
  When valid: Only when interacting with specialized external command-line binaries that provide no language-level bindings.
- Implement custom recursive directory traversal and file unlinking algorithms (rejected)
  Rejected because: Custom recursive logic introduces maintenance overhead, edge-case defects with symbolic links, and verbose boilerplate code.
  When valid: When custom in-flight filtering or deep semantic validation of individual file descriptors is required during deletion.

## Risks

- Unintended directory tree deletion if path resolution resolves to an unexpected or root directory.
  Mitigation: Validate resolved directory paths against allowed workspace boundaries before invoking recursive tree deletion.
  Owner: engineering team
- Silent failure during directory cleanup if error suppression is enabled unconditionally.
  Mitigation: Pair directory cleanup routines with diagnostic logging or targeted exception handling.
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
- Verify that target directories are explicitly constructed with required parent paths before calling shutil.move to prevent moving files into unintended locations.
- Use shutil.rmtree with targeted exception handling or controlled error parameters when purging test output directories to prevent transient file lock issues on varied host systems.

## Continuation Context

Verify commands:

- Discover the project test execution runner from the build manifest and execute the test suite covering build and test utility modules.
- Discover the code quality and static analysis runner configured in the repository and verify that all filesystem operations conform to linting and style rules.

Accept when:

- All automated test suites for development tooling and test fixtures pass cleanly with zero directory manipulation errors.
- Static analysis checks confirm that high-level directory cleanup and relocation routines use shutil rather than shell subprocess invocations.

## Enforcement

- Verified by: Automated continuous integration pipeline checks and test suite executions.
- Verified by: Peer code review for all modifications touching development tooling, documentation generation, and testing harness utilities.
- Violation handling: Pull requests containing platform-specific shell subprocess calls for filesystem management are flagged during review and blocked until converted to shutil operations.
- Violation handling: Continuous integration test failures caused by unhandled filesystem manipulation exceptions must be resolved prior to merging.
- Exception process: Exceptions require approval from the engineering team lead and documented justification explaining why platform-native or external process invocation is mandatory.
