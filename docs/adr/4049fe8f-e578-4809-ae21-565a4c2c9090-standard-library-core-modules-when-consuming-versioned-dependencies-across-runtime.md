---
version: 2
name: 4049fe8f-e578-4809-ae21-565a4c2c9090-standard-library-core-modules-when-consuming-versioned-dependencies-across-runtime
description: "Standard Library Core Modules: When Consuming Versioned Dependencies Across Runtime"
metadata:
  actual:
    adr_id: 4049fe8f-e578-4809-ae21-565a4c2c9090
    status: accepted
    candidate_group_id: e3e3a60d-aa58-47d8-82e5-7865894075f6
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/**
---

# Standard Library Core Modules: When Consuming Versioned Dependencies Across Runtime

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `src/airflowctl/**`

## Context

- The command-line interface and API client components require environment variable resolution, JSON configuration parsing, and credential prompting.
- The codebase relies on built-in standard library core modules rather than introducing third-party packages for system interactions and serialization.
- Using core runtime modules provides baseline capabilities without increasing dependency overhead across command execution and transport boundaries.

## Problem Statement

Command-line operations and client transport components need standardized system interaction, data serialization, and credential collection without introducing unvetted external dependencies that increase maintenance overhead and supply chain vulnerability surface.

## Decision

- **R-LM-001** MUST: When consuming versioned dependencies across runtime boundaries, the consumer MUST inspect the project lock file to verify resolved dependency versions prior to integration.

## Policy Block

In scope:

- Command-line execution workflows, authentication handlers, and client boundary modules requiring operating system access and data serialization.

Out of scope:

- Specialized third-party integrations requiring advanced cryptographic operations or distributed secret vault retrieval protocols.

## Rationale

- Static analysis identifies core runtime utilities providing environment variable reading, credential acquisition, and JSON serialization across client and command boundaries.
- Standard library core modules eliminate external supply chain exposure and reduce package installation overhead for common system operations.
- Relying on standard runtime capabilities maintains consistency across disparate invocation contexts without external dependencies.

## Consequences

Positive:

- Zero external dependency footprint for core system configuration and serialization tasks.
- Reduced exposure to third-party package vulnerabilities and supply-chain drift.
- Consistent baseline behavior across platforms supported by the language runtime.

Negative:

- Standard library modules offer limited high-level abstractions for complex configuration validation or secret masking.
- Manual error handling and formatting are required when interfacing with raw system environment variables.

## Alternatives

- Adopting third-party configuration and credential management frameworks (rejected)
  Rejected because: Adds external dependencies and potential maintenance overhead for basic client and command execution needs.
  When valid: Valid when complex multi-source layered configuration, automatic schema validation, or external vault synchronizations are required.

## Risks

- Unstructured environment access may lead to scattered configuration parsing and unvalidated runtime state.
  Mitigation: Encapsulate environment lookups and serialization inside designated client configuration boundaries.
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
- Ensure standard library serialization routines handle character encoding and file streaming safely across different target host environments.
- Centralize environment variable name constants within dedicated settings modules to avoid duplicate keys across commands.

## Continuation Context

Verify commands:

- Discover and run the repository test suite to verify client authentication and configuration loading behavior.
- Discover and run the project static analysis and linting verification scripts to ensure dependency boundary adherence.

Accept when:

- All configuration parsing and system utility interactions pass automated test suites without unexpected runtime exceptions.
- Static analysis checks pass without flagging unvetted external dependencies for core system interactions.

## Enforcement

- Verified by: Automated continuous integration test suite execution.
- Verified by: Peer code review verification of new dependency introductions.
- Violation handling: Pull requests introducing redundant external libraries for standard library capabilities are blocked until remediated.
- Exception process: Architectural review and written justification submitted to the engineering team when standard library modules lack essential capabilities.
