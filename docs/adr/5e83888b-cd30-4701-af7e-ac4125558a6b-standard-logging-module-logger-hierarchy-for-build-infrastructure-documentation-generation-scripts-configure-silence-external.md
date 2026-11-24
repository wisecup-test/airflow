---
version: 2
name: 5e83888b-cd30-4701-af7e-ac4125558a6b-standard-logging-module-logger-hierarchy-for-build-infrastructure-documentation-generation-scripts-configure-silence-external
description: "Standard logging Module Logger Hierarchy for Build Infrastructure: Documentation Generation Scripts Configure Silence External"
metadata:
  actual:
    adr_id: 5e83888b-cd30-4701-af7e-ac4125558a6b
    status: accepted
    candidate_group_id: ce46c2ca-4441-4f8e-b768-7290cfe59bf4
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - docs/**
      - hatch_build.py
---

# Standard logging Module Logger Hierarchy for Build Infrastructure: Documentation Generation Scripts Configure Silence External

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `docs/**`
- `hatch_build.py`

## Context

- Build automation and documentation generation scripts require diagnostic reporting during compilation, cleaning, and metadata extraction phases.
- Direct output stream printing prevents granular verbosity filtering and interferes with machine-readable build outputs.
- Multiple infrastructure modules require independent logger namespaces to selectively silence third-party library chatter or elevate build debugging details.
- Environment variables provide decoupled runtime configuration for logging verbosity across diverse automation environments.

## Problem Statement

Automated build, packaging, and documentation routines generate diagnostic and error telemetry that must be dynamically adjustable across local development and continuous integration environments without modifying source code or polluting standard output streams.

## Decision

- **R-L-001** SHOULD: Documentation and generation scripts SHOULD configure or silence external subsystem loggers by acquiring their specific named logger instances through getLogger.

## Policy Block

In scope:

- Build hook scripts, packaging automation routines, and documentation configuration modules within the repository.
- Operational tooling scripts that execute during build, compilation, or asset preparation phases.

Out of scope:

- Core runtime application logging configurations governed by dedicated service observability frameworks.
- Pure test assertion helpers that rely on test runner output capture facilities.

Exceptions:

- **EXC-46-001** A command-line execution phase requires unformatted standard output intended directly for shell pipe ingestion.
  Approval: Architecture review board or core infrastructure maintainers
  Documentation: Document the stream redirection requirement in the script header comments.

## Rationale

- The logging module provides standardized level filtering and namespace hierarchy that raw output streams cannot support.
- Inspecting environment variables at startup enables dynamic log level adjustments in automated environments without requiring code modifications.
- Scoped getLogger identifiers allow selective suppression of verbose third-party generators while preserving critical build diagnostic messages.

## Consequences

Positive:

- Standardizes diagnostic output across build and documentation scripts using structured log levels.
- Enables fine-grained verbosity control across automated execution environments via environment variables.
- Isolates third-party generator log noise by targeted namespace configuration.

Negative:

- Requires explicit logger instantiation boilerplate across utility and build scripts.
- Introduces subtle verbosity mismatches if environment variable naming conventions diverge across scripts.

## Alternatives

- Standard output printing functions (rejected)
  Rejected because: Lacks hierarchical filtering, verbosity levels, and environment-driven threshold adjustment.
  When valid: Trivial one-off scripts executed strictly in interactive terminal environments.
- Third-party specialized logging libraries (rejected)
  Rejected because: Adds external dependency overhead to build bootstrap and documentation preparation scripts.
  When valid: Long-running runtime microservices requiring distributed tracing and external log shipping.

## Risks

- Failure to set an appropriate log level threshold may silence critical build warnings or pollute CI logs.
  Mitigation: Provide safe default fallback thresholds when environment variables are not supplied.
  Owner: Infrastructure Team
- Inconsistent logger naming across scripts impedes central log level filtering.
  Mitigation: Enforce standard module naming conventions for logger instantiation during code review.
  Owner: Core Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Ensure environment variable lookups for build log level configuration default to standard informational thresholds when the variable is absent.
- When targeting third-party documentation mappers, acquire the specific logger namespace and apply the desired log level prior to triggering generation hooks.

## Continuation Context

Verify commands:

- Discover and run the repository static analysis and linting scripts to verify logger imports and check that raw print statements are absent from infrastructure scripts.
- Discover and run the build validation pipeline with custom log level environment variables configured to verify dynamic threshold propagation.

Accept when:

- Static analysis validates that all build and documentation scripts utilize getLogger rather than raw standard stream printing.
- Executing build verification scripts with elevated log level environment variables displays expected diagnostic log messages.

## Enforcement

- Verified by: Automated static analysis checks in continuous integration pipelines.
- Verified by: Peer code review for changes affecting build and documentation scripts.
- Violation handling: Pull requests containing direct standard stream printing in infrastructure scripts will fail automated linting and require remediation.
- Exception process: Submit an architectural review request detailing why structured logging cannot meet specific CLI or stream piping requirements.
