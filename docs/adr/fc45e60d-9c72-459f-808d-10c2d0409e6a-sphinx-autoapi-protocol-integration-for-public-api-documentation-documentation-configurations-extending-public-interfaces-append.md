---
version: 2
name: fc45e60d-9c72-459f-808d-10c2d0409e6a-sphinx-autoapi-protocol-integration-for-public-api-documentation-documentation-configurations-extending-public-interfaces-append
description: "sphinx.autoapi Protocol Integration for Public API Documentation: Documentation Configurations Extending Public Interfaces Append"
metadata:
  actual:
    adr_id: fc45e60d-9c72-459f-808d-10c2d0409e6a
    status: accepted
    candidate_group_id: c710f170-d389-4e46-ba0e-4d7af4346fbe
    evidence:
      source: deep
  scope:
    root: docker-stack-docs
---

# sphinx.autoapi Protocol Integration for Public API Documentation: Documentation Configurations Extending Public Interfaces Append

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `docker-stack-docs`

Applies to:
- `**` (whole root)

## Context

- The codebase exposes public external APIs that require automated reference documentation extracted directly from source modules.
- During API object mapping, internal implementation details and unexported symbols generate unwanted mapper log entries and index clutter if left unfiltered.
- Custom documentation extensions and dynamic HTML context helpers must be integrated through structured hooks to maintain consistent build outputs across package variants.

## Problem Statement

Automated public API documentation extraction can inadvertently expose internal symbols and trigger excessive mapper warnings unless filtering rules are attached directly to the mapper logging pipeline. A standardized pattern is needed to filter mapper entries, extend documentation capabilities, and dynamically supply HTML context without polluting global namespace state.

## Decision

- **R-PEA-001** MUST: Documentation configurations extending public API interfaces MUST append custom substitution handlers using extensions.extend with extra_files_with_substitutions rather than mutating global extension registries directly.

## Policy Block

In scope:

- Public documentation generation configurations defining external API references.
- Logging filter integrations managing public API object extraction.

Out of scope:

- Internal documentation modules that do not publish external public API surfaces.
- Standard application runtime code paths outside documentation pipelines.

## Rationale

- Attaching filter_autoapi_ignore_entries via addFilter on the sphinx.autoapi.mappers.base logger intercepts unwanted entity extraction at the source, preventing documentation noise.
- Using structured extension registration through extensions.extend and modular context functions preserves encapsulation across documentation builds.
- Deriving package documentation parameters from runtime environment variables allows a single documentation configuration to adapt to multiple package targets.

## Consequences

Positive:

- Clean public API documentation output with internal implementation details consistently excluded.
- Elimination of spurious mapper warnings during documentation generation.
- Centralized and decoupled configuration for external API documentation extraction.

Negative:

- Tightly couples documentation filtering logic to internal logger naming conventions of the documentation extraction library.
- Requires ongoing verification against library updates to ensure mapper logger identifiers and filter method signatures remain stable.

## Alternatives

- Manual maintenance of public API reference files without automated mapper extraction (rejected)
  Rejected because: Manual documentation introduces high maintenance overhead, synchronization drift, and frequent omissions of newly exposed public API members.
  When valid: Small static projects with minimal public API surfaces that change infrequently.
- Global suppression of all documentation generator warnings (rejected)
  Rejected because: Suppressing all warnings obscures critical syntax errors, broken cross-references, and missing docstrings across the documentation suite.
  When valid: Never valid in production documentation builds where interface correctness is required.

## Risks

- Upstream updates to the documentation generator may alter the internal logger name sphinx.autoapi.mappers.base, causing filters to silently detach.
  Mitigation: Include automated assertions in documentation test suites that verify filtered symbols are absent from build artifacts.
  Owner: Documentation Engineering Team
- Runtime environment variable mismatches during documentation compilation could cause inconsistent context generation across build environments.
  Mitigation: Enforce validation of required environment configuration variables with descriptive fallback defaults during initialization.
  Owner: Documentation Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Discover the project documentation configuration module and locate the logger instance attached to sphinx.autoapi.mappers.base to attach custom filtering logic.
- Inspect the project dependency manifest and lock artifact to identify the supported version range for sphinx.autoapi before configuring logging filter handlers.

## Continuation Context

Verify commands:

- Discover and execute the project documentation build test suite to verify that API documentation builds without unresolved mapper warnings.
- Discover and run the project static analysis and linting verification scripts to validate that logging filter attachments conform to required interface contracts.
- Inspect generated documentation output artifacts to verify that ignored internal entities are excluded from the public API documentation index.

Accept when:

- The documentation build verification completes with exit status zero and produces no warnings from the public API mapper logger.
- Public API documentation output excludes entries designated by the filter function.
- All documentation configuration calls adhere to resolved dependency interfaces established in the project lock artifact.

## Enforcement

- Verified by: Automated documentation build test suites executed in continuous integration workflows.
- Verified by: Code review verification checking adherence to logging filter patterns and extension interfaces.
- Violation handling: Pull requests introducing unhandled public API documentation mapper warnings or malformed filter attachments will fail continuous integration checks and be blocked from merging.
- Violation handling: Deviations require revision to align with standard documentation configuration patterns.
- Exception process: Exceptions require approval from the documentation architecture maintainers and must be documented with an architectural justification in the repository issue tracker.
