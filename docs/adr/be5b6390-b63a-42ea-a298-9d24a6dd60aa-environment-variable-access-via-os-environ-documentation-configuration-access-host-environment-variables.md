---
version: 2
name: be5b6390-b63a-42ea-a298-9d24a6dd60aa-environment-variable-access-via-os-environ-documentation-configuration-access-host-environment-variables
description: "Environment Variable Access via os.environ: Documentation Configuration Access Host Environment Variables"
metadata:
  actual:
    adr_id: be5b6390-b63a-42ea-a298-9d24a6dd60aa
    status: accepted
    candidate_group_id: 3c498080-850e-4117-bdf0-a12dc4d37e55
    evidence:
      source: deep
  scope:
    root: providers/apache/druid
    paths:
      - docs/**
---

# Environment Variable Access via os.environ: Documentation Configuration Access Host Environment Variables

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `providers/apache/druid`

Applies to:
- `docs/**`

## Context

- Documentation generation processes require package identification metadata provided dynamically by external build environments.
- The codebase reads runtime environment values using the standard library os.environ mapping.
- Static analysis flagged direct os.environ indexing under the secrets handling facet despite the value representing non-sensitive package identification metadata.

## Problem Statement

Build processes must reliably inject package identification parameters into documentation configuration without over-engineering non-sensitive configuration into secret management pipelines.

## Decision

- **R-SC-001** MUST: The documentation configuration MUST access host environment variables through the standard os.environ mapping when resolving package metadata at generation time.

## Policy Block

In scope:

- Documentation build configuration scripts requiring package identity parameters from the execution environment.

Out of scope:

- Production credential management, authentication secrets, and sensitive token handling.

## Rationale

- Using the standard library os.environ mapping provides immediate access to execution environment variables without introducing third-party dependencies.
- Reading non-sensitive package metadata from the environment decouples documentation configuration from hardcoded package names.

## Consequences

Positive:

- Eliminates hardcoded package names across documentation configuration.
- Requires zero third-party dependencies for reading host execution configuration.

Negative:

- Direct dictionary indexing throws an unhandled error if the expected environment variable is unset.
- May trigger static analysis false positives that classify benign configuration parameters as secrets handling.

## Alternatives

- Dedicated Secret Management Vault Integration (rejected)
  Rejected because: Over-engineering for non-sensitive package metadata required only during documentation generation.
  When valid: When handling actual sensitive production credentials, API tokens, or private keys.
- Structured Configuration File Loading (rejected)
  Rejected because: Adds file parsing overhead when an environment variable injected by the build harness suffices.
  When valid: When managing complex hierarchical configurations across multiple documentation modules.

## Risks

- Missing environment variables causing unhandled exceptions during execution.
  Mitigation: Ensure the build harness injects required keys or validate key existence before indexing.
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
- Ensure environment variables required for documentation generation are documented in repository developer guides and provided in CI build runners.
- Distinguish between non-sensitive environment configuration and genuine secrets to prevent misclassification in security audits.

## Continuation Context

Verify commands:

- Discover and run the project static analysis and linting suite to check for unhandled environment variable access patterns.
- Discover and execute the repository documentation build workflow to verify environment variable resolution passes without errors.

Accept when:

- Static analysis checks pass with zero unhandled environment variable access errors.
- Documentation build passes successfully when required environment variables are set.

## Enforcement

- Verified by: Automated static analysis in CI pipelines
- Verified by: Code review approval prior to merging
- Violation handling: Flagged pull requests must be corrected or reviewed for false-positive secret classification.
- Exception process: Architectural review approval for specialized configuration injection requirements.
