---
version: 2
name: 116bec6d-f099-446e-89d8-40b6c678da0c-click-custom-parameter-type-specialization-command-handlers-not-implement-manual-string
description: "click Custom Parameter Type Specialization: Command Handlers Not Implement Manual String"
metadata:
  actual:
    adr_id: 116bec6d-f099-446e-89d8-40b6c678da0c
    status: accepted
    candidate_group_id: a08140a8-4566-4c2a-abb6-42f574c85d9c
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/commands/**
---

# click Custom Parameter Type Specialization: Command Handlers Not Implement Manual String

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/commands/**`

## Context

- Command-line interface commands require structured parsing and strict validation of user-provided options across diverse operational modes.
- Standard raw string parameters fail to enforce domain-specific constraints early, leading to repetitive manual validation inside command execution handlers.
- Different command options require distinct constraint behaviors, including static enumerations, dynamically evaluated distributions, interactive affirmative or negative answers, and cached backend configurations.
- The codebase adopted click for command-line parsing and established custom parameter types to bridge command option definitions with domain validation logic.

## Problem Statement

Command-line option definitions without standardized constraint types allow arbitrary inputs that require redundant downstream validation and error handling inside command bodies. Furthermore, native choice validation does not support caching expensive option resolution or handling dynamic distribution lists, risking inconsistent option validation, confusing error messaging, and runtime failures across command invocations.

## Decision

- **R-LM-001** MUST_NOT: Command handlers MUST NOT implement manual string matching or manual value validation when equivalent constraints can be expressed via custom parameter types.

## Policy Block

In scope:

- Option definitions and parameter declarations across command-line interface command modules.
- Interactive prompts and choices within command-line tools.

Out of scope:

- Internal helper utilities that do not declare or expose command-line interface options.
- Non-interactive background processes that do not accept command-line arguments.

## Rationale

- Specializing click parameter types centralizes validation logic at the command interface boundary, ensuring invalid inputs are rejected before command execution begins.
- Utilizing BetterChoice and NotVerifiedBetterChoice ensures consistent error formatting and option discovery across all CLI commands.
- Employing CacheableChoice eliminates repetitive computation for options derived from dynamic environments while retaining strict validation.
- Querying ctx.get_parameter_source enables command logic to distinguish between configuration defaults, environment settings, and explicit user flags reliably.

## Consequences

Positive:

- Consistent input validation and unified error reporting across all command-line options.
- Elimination of boilerplate validation code inside command handler functions.
- Improved performance through cached evaluation of dynamic option values via CacheableChoice.
- Predictable option provenance inspection via ctx.get_parameter_source.

Negative:

- Increased conceptual overhead for developers who must learn custom choice classes instead of standard primitives.
- Tight coupling of option definitions to internal parameter type wrappers.
- Maintenance overhead required to keep custom parameter types compatible with upstream click releases.

## Alternatives

- Use standard click Choice parameter types directly for all bounded options (rejected)
  Rejected because: Standard choice implementations do not support caching expensive evaluations, interactive answer handling, or dynamic unverified distribution sets.
  When valid: Valid for simple static options where dynamic evaluation and specialized formatting are not required.
- Validate option values manually inside each command handler body (rejected)
  Rejected because: Scatters validation logic across handlers, creates inconsistent error messages, and increases test duplication.
  When valid: Valid when validation rules depend on complex multi-parameter interactions that cannot be resolved independently during parameter parsing.

## Risks

- Upstream updates to click internal parameter parsing could introduce breaking changes to custom parameter types.
  Mitigation: Lock the exact dependency version in the lock artifact and execute project integration test suites before upgrading.
  Owner: engineering team
- Developers might bypass custom parameter types in favor of raw strings, reintroducing scattered validation.
  Mitigation: Enforce usage through automated static analysis checks and pull request code reviews.
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
- Derive all parameter choices from domain constants or dynamic package resolution functions at option definition time.
- When implementing new choice options that require caching, encapsulate the value collection within CacheableChoice to minimize latency across repeated command evaluations.

## Continuation Context

Verify commands:

- Discover and execute the project test runner to validate command-line option parsing and custom parameter type behavior.
- Discover and run the project code linters to detect unvalidated string options across command modules.

Accept when:

- All command-line options requiring restricted inputs utilize specialized choice parameter types and reject invalid values with standardized error messages.
- All test suites verifying command option parsing and parameter source inspection pass successfully.

## Enforcement

- Verified by: Automated test suites executing command-line parsing verification.
- Verified by: Static analysis checks enforcing parameter typing conventions.
- Verified by: Peer code review on all pull requests modifying command option definitions.
- Violation handling: Pull requests introducing unconstrained primitive parameters for bounded options are blocked until converted to custom parameter types.
- Violation handling: Continuous integration failures when parameter validation tests do not pass.
- Exception process: Exceptions require approval from the tooling maintainers with written rationale explaining why domain validation cannot be handled by custom parameter types.
