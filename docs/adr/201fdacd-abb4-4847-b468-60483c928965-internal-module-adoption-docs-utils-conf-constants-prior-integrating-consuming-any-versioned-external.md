---
version: 2
name: 201fdacd-abb4-4847-b468-60483c928965-internal-module-adoption-docs-utils-conf-constants-prior-integrating-consuming-any-versioned-external
description: "Internal Module Adoption: docs.utils.conf_constants: Prior Integrating Consuming Any Versioned External"
metadata:
  actual:
    adr_id: 201fdacd-abb4-4847-b468-60483c928965
    status: accepted
    candidate_group_id: fffe84cb-e2bd-4103-ad75-9d1c5b4d020f
    evidence:
      source: deep
  scope:
    root: docker-stack-docs
---

# Internal Module Adoption: docs.utils.conf_constants: Prior Integrating Consuming Any Versioned External

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `docker-stack-docs`

Applies to:
- `**` (whole root)

## Context

- Centralized documentation configuration ensures consistent layout and sidebar structure across builds.
- Documentation modules require shared metadata extraction routines such as get_html_sidebars and get_html_context to standardize navigation contexts.
- Configuration scripts rely on runtime environment variables to condition build behaviors while delegating structural constants to shared utility modules.

## Problem Statement

Documentation configurations across complex multi-package repositories risk configuration divergence and duplicate navigation logic if individual build targets independently define context generation rules, sidebars, and version constants.

## Decision

- **R-LM-001** MUST: Prior to integrating or consuming any versioned external dependency, the consumer MUST inspect the repository lock or resolution artifact to determine and verify the exact resolved version.

## Policy Block

In scope:

- Build configuration modules requiring shared documentation metadata and context generation.

Out of scope:

- Standalone application source modules outside the documentation build pipeline.

Exceptions:

- **EXC-20-001** A documentation target requires specialized, non-standard layout rules unhandled by shared helpers.
  Approval: Architecture Review Board
  Documentation: Document exception within the local configuration comments.

## Rationale

- Centralizing documentation metadata within docs.utils.conf_constants eliminates duplicated sidebar and context mapping across individual documentation targets.
- Invoking get_html_sidebars and get_html_context through a shared utility module guarantees consistent navigation rendering and version tracking across build processes.
- Static evidence demonstrates explicit delegation of environment-derived configuration variables to docs.utils.conf_constants.

## Consequences

Positive:

- Enforces consistent documentation sidebars and context generation across documentation build targets.
- Centralizes version resolution logic, reducing the risk of broken cross-references during documentation assembly.
- Simplifies maintenance by decoupling documentation layout definitions from individual configuration targets.

Negative:

- Couples documentation configuration directly to the internal API surface of docs.utils.conf_constants.
- Restricts custom sidebar formatting for individual documentation packages without shared helper modifications.

## Alternatives

- Inline duplicate context and sidebar definitions within each documentation configuration script (rejected)
  Rejected because: Causes navigation inconsistencies and multiplies maintenance overhead across documentation builds.
  When valid: Valid only in completely decoupled, single-page documentation prototypes.
- Externalize documentation configuration entirely into static data files (rejected)
  Rejected because: Lacks dynamic evaluation capabilities needed for version-sensitive context derivation and conditional sidebar generation.
  When valid: Valid when documentation layout contains no version-conditioned navigation trees.

## Risks

- Breaking changes in docs.utils.conf_constants helper signatures can fail downstream documentation builds.
  Mitigation: Maintain backward-compatible helper signatures and enforce contract testing in automated build pipelines.
  Owner: engineering team
- Missing runtime environment variables may cause unexpected fallback states in context resolution.
  Mitigation: Enforce explicit default values and runtime validation for all required environment parameters.
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
- Import get_html_sidebars and get_html_context from docs.utils.conf_constants to populate documentation layout context structures.
- Validate required runtime environment configuration flags during initialization before invoking context generation helpers.

## Continuation Context

Verify commands:

- Discover and run the project documentation build and verification script to confirm configuration integrity.
- Discover and run the project static analysis or test suite to verify module import resolution.

Accept when:

- The documentation build pipeline completes without errors and correctly resolves navigation sidebars.
- Static verification confirms that all context helper invocations adhere to the shared module interfaces.

## Enforcement

- Verified by: Automated continuous integration documentation build checks.
- Verified by: Peer code review for changes affecting documentation configuration modules.
- Violation handling: Documentation build failures in continuous integration block merge requests.
- Violation handling: Code review rejection for hardcoded sidebar configurations.
- Exception process: Exceptions must be submitted via architectural review and documented in the repository configuration tracking log.
