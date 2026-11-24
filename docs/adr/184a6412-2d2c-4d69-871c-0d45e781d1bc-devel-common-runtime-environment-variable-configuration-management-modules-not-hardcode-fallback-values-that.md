---
version: 2
name: 184a6412-2d2c-4d69-871c-0d45e781d1bc-devel-common-runtime-environment-variable-configuration-management-modules-not-hardcode-fallback-values-that
description: "devel-common Runtime Environment Variable Configuration Management: Modules Not Hardcode Fallback Values That"
metadata:
  actual:
    adr_id: 184a6412-2d2c-4d69-871c-0d45e781d1bc
    status: accepted
    candidate_group_id: a119244d-841c-40b7-af5a-4e263ca819d7
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/docs/**
      - src/sphinx_exts/**
---

# devel-common Runtime Environment Variable Configuration Management: Modules Not Hardcode Fallback Values That

Date: 2025-05-15
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/docs/**`
- `src/sphinx_exts/**`

## Context

- Shared internal development and documentation modules require runtime parameters derived from the host process execution context.
- Operational parameters such as target package identifiers and build-phase indicators vary across invocation environments and must be injected dynamically without modifying source definitions.
- Cache directory locations must conform to host environment standards while providing fallback defaults when explicit paths are not declared.
- Existing internal tooling directly interrogates process-level environment sources to configure execution behavior and file resolution logic.

## Problem Statement

Execution-time parameters for internal developer tooling and documentation build modules vary across local developer environments and automated continuous integration pipelines. Without standardized conventions for reading process environment variables, modules risk inconsistent fallback handling, silent execution with uninitialized variables, and fragmented access patterns across different subpackages.

## Decision

- **R-CEM-001** MUST_NOT: Modules MUST NOT hardcode fallback values that violate standard operating system directory conventions when resolving cache or state directories.

## Policy Block

In scope:

- Internal developer tooling modules within devel-common
- Documentation generation and extension configuration scripts
- Local and pipeline execution environments where dynamic runtime context is supplied via environment variables

Out of scope:

- Production runtime service kernels requiring persistent database-backed configuration
- Static configuration declarations that do not vary between build and execution environments

Exceptions:

- **EX-37-001** A module requires complex hierarchical configuration structures that cannot be modeled cleanly through individual environment variables
  Approval: Architecture review by the tooling maintainers
  Documentation: Document the structured configuration file format and loading hierarchy in the module design documentation

## Rationale

- Direct interrogation of process environment variables via os.environ and os.getenv eliminates the need for heavyweight configuration frameworks in lightweight internal developer utilities.
- Standardizing key-indexed access for mandatory variables enforces fail-fast semantics during builds, preventing cascading failures downstream.
- Providing deterministic defaults via os.getenv ensures reliable operation in developer workstations while allowing automated pipelines to inject overrides seamlessly.

## Consequences

Positive:

- Eliminates external framework dependencies for configuration management in developer tooling modules.
- Enables consistent context propagation between automated CI orchestrators and documentation build scripts.
- Ensures fail-fast behavior when critical runtime environment variables are missing.

Negative:

- Direct process environment access scatters configuration keys across individual call sites rather than centralizing them in a single typed schema.
- Missing type validation requires call sites to manually handle type conversion and string parsing.
- Environment variable names lack compile-time discovery, increasing reliance on documentation and runtime checks.

## Alternatives

- Adopt a centralized third-party configuration library with typed data schemas (rejected)
  Rejected because: Introduces unnecessary external dependency overhead and initialization complexity for lightweight build and documentation scripts
  When valid: Valid when application services require multi-layered configuration cascading across files, remote secret stores, and environment variables
- Pass all configuration options exclusively through command-line invocation flags (rejected)
  Rejected because: Requires forwarding command-line arguments through multiple layers of nested tool invocations and documentation generators
  When valid: Valid for standalone CLI utilities with flat argument parsing requirements

## Risks

- Unset mandatory environment variables causing unhandled exceptions during execution
  Mitigation: Enforce explicit key presence validation with descriptive exception messages upon startup
  Owner: engineering team
- Implicit divergence between local development workstation environments and continuous integration pipelines
  Mitigation: Provide sensible default paths for local execution while documenting required pipeline variables
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
- Locate the repository dependency manifest and lock artifact to inspect resolved dependency versions for all build and execution tooling.
- When constructing file system cache paths from environment variables, resolve user directory expansions and verify directory creation permissions before writing assets.
- Ensure all environment variable access is centralized at module import or initialization entry points rather than repeatedly evaluating process state inside loops.

## Continuation Context

Verify commands:

- Discover the project test execution runner from repository configuration and execute the unit test suite covering configuration and environment handling.
- Discover and run the repository static analysis and linting scripts to verify compliance with environment access conventions.
- Execute the documentation generation workflow in an environment with clean configuration variables to verify fallback resolution.

Accept when:

- The project test suite passes with all environment resolution tests succeeding.
- Static analysis checks pass with no unhandled environment access violations.
- Documentation generation succeeds with default environment fallback paths.

## Enforcement

- Verified by: Automated continuous integration pipeline execution covering test suites
- Verified by: Static analysis rule checks on pull requests
- Verified by: Peer code review verification of environment access patterns
- Violation handling: Pull requests with unvalidated environment variable access or missing fallbacks are blocked from merging until corrected
- Violation handling: Build failures triggered by missing environment variables must be resolved by adding explicit exceptions or defaults
- Exception process: Submit an architectural review request to the tooling maintainers outlining the rationale for structured configuration alternatives
- Exception process: Record accepted exceptions in the module policy registry
