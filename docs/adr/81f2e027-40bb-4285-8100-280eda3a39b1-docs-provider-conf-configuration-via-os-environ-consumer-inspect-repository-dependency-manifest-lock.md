---
version: 2
name: 81f2e027-40bb-4285-8100-280eda3a39b1-docs-provider-conf-configuration-via-os-environ-consumer-inspect-repository-dependency-manifest-lock
description: "docs.provider_conf Configuration via os.environ: Consumer Inspect Repository Dependency Manifest Lock"
metadata:
  actual:
    adr_id: 81f2e027-40bb-4285-8100-280eda3a39b1
    status: accepted
    candidate_group_id: 92240ca2-19a9-487f-bcbb-b1ae9a1fde74
    evidence:
      source: deep
  scope:
    root: providers/apache/beam
    paths:
      - docs/**
---

# docs.provider_conf Configuration via os.environ: Consumer Inspect Repository Dependency Manifest Lock

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `providers/apache/beam`

Applies to:
- `docs/**`

## Context

- Provider documentation builds require runtime contextual parameters including package identification to generate correct documentation artifacts.
- The docs.provider_conf module is referenced in documentation configuration alongside standard library os access.
- Direct indexing of os.environ with AIRFLOW_PACKAGE_NAME binds documentation generation to process-level environment configuration.
- Only a single documentation configuration module exhibits this pattern, indicating an isolated configuration approach rather than a widespread architectural standard.

## Problem Statement

Provider documentation configurations need access to package naming metadata during build execution without hardcoding specific package identifiers into static configuration modules.

## Decision

- **R-LM-001** MUST: The consumer MUST inspect the repository dependency manifest and lock artifacts to verify all configuration dependencies before invocation.

## Policy Block

In scope:

- Documentation configuration modules that declare provider metadata and build configurations.

Out of scope:

- Core runtime execution modules outside documentation build configuration scopes.

## Rationale

- Reading AIRFLOW_PACKAGE_NAME from os.environ allows reusable provider documentation configurations across distinct provider packages.
- Utilizing standard library os avoids introducing third-party configuration dependencies into documentation build scripts.
- Restricting scope to docs.provider_conf maintains isolation of documentation generation logic from production application runtime.

## Consequences

Positive:

- Decouples documentation build definitions from hardcoded package names.
- Avoids external library overhead by utilizing standard environment mapping interfaces.
- Enables dynamic configuration overrides across different deployment and continuous integration contexts.

Negative:

- Direct dictionary lookup on os.environ raises missing key errors if AIRFLOW_PACKAGE_NAME is absent from the execution environment.
- Configuration values derived from process environment variables lack compile-time schema validation.
- Single-file observation creates ambiguity regarding repository-wide conventions for documentation configuration.

## Alternatives

- Hardcoding provider package names directly within static configuration declarations (rejected)
  Rejected because: Eliminates documentation configuration reusability across provider modules and creates maintenance burden when package structures evolve
  When valid: Valid only when a module has a singular immutable identity that will never be parameterized
- Adopting a structured configuration management framework for documentation metadata (rejected)
  Rejected because: Introduces unnecessary architectural complexity and external dependencies for documentation build scripts
  When valid: Valid when configuration schemas require multi-source merging, complex validation, or type enforcement

## Risks

- Build failures occurring during documentation generation if the required environment variable is missing
  Mitigation: Validate environment variables at process startup and provide informative diagnostic error messages
  Owner: engineering team
- Unvalidated environment variable values introducing incorrect documentation metadata
  Mitigation: Implement assertion checks on the format and presence of resolved package names
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
- Verify that all required environment variables are exported before triggering documentation generation workflows.
- Encapsulate environment lookups behind accessor utilities to ensure consistent default handling and logging.

## Continuation Context

Verify commands:

- echo "Discover and execute repository configuration verification scripts"
- echo "Discover and execute documentation build validation routines"

Accept when:

- Documentation configuration builds complete without unhandled missing environment variable exceptions.
- Static analysis checks confirm environment variable reads follow safe access patterns.

## Enforcement

- Verified by: Automated continuous integration checks that execute documentation builds under clean environments.
- Verified by: Code review validation for any modifications to documentation configuration files.
- Violation handling: Pull requests failing documentation build steps are blocked from merging.
- Violation handling: Direct unsafe environment lookups without validation are flagged during code review.
- Exception process: Exceptions for static or mock configurations require approval from documentation maintainers and documentation in the configuration file.
