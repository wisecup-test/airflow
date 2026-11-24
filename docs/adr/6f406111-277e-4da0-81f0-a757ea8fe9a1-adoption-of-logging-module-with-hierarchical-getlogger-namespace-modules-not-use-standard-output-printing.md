---
version: 2
name: 6f406111-277e-4da0-81f0-a757ea8fe9a1-adoption-of-logging-module-with-hierarchical-getlogger-namespace-modules-not-use-standard-output-printing
description: "Adoption of logging Module with Hierarchical getLogger Namespace: Modules Not Use Standard Output Printing"
metadata:
  actual:
    adr_id: 6f406111-277e-4da0-81f0-a757ea8fe9a1
    status: accepted
    candidate_group_id: 88a7d30c-cdac-4133-af32-d8bc5ca3dd47
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - assign_cherry_picked_prs_with_milestone.py
      - prepare_bulk_issues.py
      - stats/**
---

# Adoption of logging Module with Hierarchical getLogger Namespace: Modules Not Use Standard Output Printing

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `assign_cherry_picked_prs_with_milestone.py`
- `prepare_bulk_issues.py`
- `stats/**`

## Context

- Developer automation and data aggregation utilities require structured diagnostic observability during batch processing and repository maintenance operations.
- Direct standard stream writes and root logger invocations mix diagnostic output with programmatic output and prevent hierarchical filtering across disparate utility modules.
- Multiple administrative scripts uniformly standardize on the core logging module using module-scoped loggers instantiated via logging.getLogger(__name__).

## Problem Statement

Administrative and developer utility scripts require a consistent observability mechanism that isolates module log emissions, avoids polluting standard output streams used for data piping, and prevents global state mutations inherent in un-scoped root logging.

## Decision

- **R-LM-001** MUST_NOT: Modules MUST NOT use standard output printing for operational diagnostics, warnings, or error reporting.

## Policy Block

In scope:

- Developer utility scripts, maintenance tasks, and data extraction modules executing in the project environment.

Out of scope:

- Interactive terminal interface components where formatted presentation libraries manage output streams directly.

## Rationale

- Hierarchical namespace isolation using logging.getLogger(__name__) maps loggers directly to module dot-notation paths, enabling targeted log level filtering without modifying source code.
- Preventing root logger pollution guarantees that third-party modules or downstream subroutines do not inadvertently alter global log emission configurations.
- Standardizing on the core logging interface eliminates reliance on external logging frameworks for internal maintenance scripts, minimizing maintenance overhead.

## Consequences

Positive:

- Provides granular, module-level control over logging thresholds and handlers across developer utility scripts.
- Preserves separation between machine-readable standard output streams and human-readable diagnostic messages.
- Ensures zero third-party dependency footprint for baseline observability across developer tasks.

Negative:

- Requires explicit logger instantiation in every module file instead of relying on convenient root-level logging shortcuts.
- Requires downstream test harnesses to configure appropriate log capture fixtures rather than inspecting standard output buffers.

## Alternatives

- Direct invocation of root logging module functions (rejected)
  Rejected because: Mutates shared global logging state across modules and prevents granular per-module log level configuration and hierarchical handler routing.
  When valid: Single-file throwaway prototypes with no package hierarchy or downstream consumers.
- Adoption of third-party structured logging libraries (rejected)
  Rejected because: Introduces external dependency overhead and potential version conflicts for internal maintenance utilities that are sufficiently served by the standard library.
  When valid: Distributed production microservices requiring structured JSON log formatting with centralized telemetry ingestion pipelines.

## Risks

- Inconsistent logger naming across submodules leading to orphaned logger hierarchies.
  Mitigation: Enforce module-level logging.getLogger(__name__) strictly via static analysis and automated code review checks.
  Owner: Engineering Team
- Unconfigured root loggers in standalone scripts causing silent suppression of diagnostic log records.
  Mitigation: Provide standardized logging initialization entry points for standalone command-line scripts to configure stream handlers at runtime.
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
- Instantiate module loggers at the top of the file immediately following standard module imports using the canonical logger declaration pattern.
- Ensure scripts executed as main entry points configure root handlers and formatters only within the conditional entry point block, leaving library modules handler-free.

## Continuation Context

Verify commands:

- Discover project static analysis commands from repository manifests and run linting against utility modules.
- Discover test runner execution scripts from project configuration and run test suite verification.

Accept when:

- Static analysis checks complete with zero errors regarding logger naming or direct root logger calls.
- All developer utility tests pass with expected log events captured by standard logging fixtures without uncaptured standard error leakage.

## Enforcement

- Verified by: Automated linting and static analysis checks executed during continuous integration workflows.
- Verified by: Peer code review verification during pull request inspection.
- Violation handling: Pull requests containing direct root logger calls or standard output diagnostic prints will fail automated checks and require remediation prior to merge.
- Exception process: Submit an architectural review request detailing technical constraints preventing standard logger hierarchy usage, requiring approval from maintainers.
