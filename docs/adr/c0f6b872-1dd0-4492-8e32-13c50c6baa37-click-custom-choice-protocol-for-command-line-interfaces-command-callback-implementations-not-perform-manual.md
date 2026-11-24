---
version: 2
name: c0f6b872-1dd0-4492-8e32-13c50c6baa37-click-custom-choice-protocol-for-command-line-interfaces-command-callback-implementations-not-perform-manual
description: "Click Custom Choice Protocol for Command-Line Interfaces: Command Callback Implementations Not Perform Manual"
metadata:
  actual:
    adr_id: c0f6b872-1dd0-4492-8e32-13c50c6baa37
    status: accepted
    candidate_group_id: 2a7bee18-98df-43fe-8591-18ced454e09b
    evidence:
      source: deep
  scope:
    root: dev
---

# Click Custom Choice Protocol for Command-Line Interfaces: Command Callback Implementations Not Perform Manual

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `**` (whole root)

## Context

- Command-line interfaces across the codebase expose numerous configuration options and execution switches to users and external scripts.
- Standard choice validation mechanisms in the underlying command-line framework lack flexibility for token normalization, dynamic completion values, and visual choice presentation.
- Subclassing framework parameter protocols allows custom formatting and input coercion while preserving framework interoperability.

## Problem Statement

Standard parameter parsing mechanisms in command-line frameworks break display formatting when handling large enumeration sets and reject dynamic values, forcing developers into fragile manual parsing within command handlers.

## Decision

- **R-PEA-001** MUST_NOT: Command callback implementations MUST NOT perform manual parsing, splitting, or string normalization on arguments where a specialized parameter protocol type is applicable.

## Policy Block

In scope:

- Public and external command-line interface parameter definitions requiring choice enumeration, custom metavar formatting, or dynamic shell completion.

Out of scope:

- Internal programmatic APIs and function contracts that do not interface with the command-line framework.

Exceptions:

- **EX-25-001** A command option requires unconstrained input data with no autocompletion or normalization requirements.
  Approval: Code Review
  Documentation: Document parameter constraints in the command callback docstring.

## Rationale

- Custom subclasses of click.Choice centralize choice formatting, token normalization, and dynamic input handling across all public CLI commands.
- Overriding get_metavar and convert preserves native shell completion while allowing unverified or formatted inputs without breaking option contracts.

## Consequences

Positive:

- Normalizes user input, case sensitivity, and token normalization uniformly before passing parameters to command functions.
- Improves command-line interface presentation by preventing formatting artifacts on extensive choice lists.
- Preserves shell auto-completion capabilities while permitting dynamic parameter values.

Negative:

- Requires maintaining custom parameter subclasses aligned with upstream Click framework internal contracts.
- Adds conceptual complexity for developers defining new command options who must choose between specialized parameter subclasses.

## Alternatives

- Standard click.Choice parameter types without customization (rejected)
  Rejected because: Standard choices break formatting across long parameter lists and reject unlisted dynamic arguments required for extensible options.
  When valid: Command options with short, fixed value lists that require no special presentation or autocomplete flexibility.
- Ad-hoc string parsing inside command callback bodies (rejected)
  Rejected because: Violates separation of concerns by duplicating argument validation and token normalization across individual command handlers.
  When valid: Single-use commands with completely unstructured input formats.

## Risks

- Upstream framework changes to parameter type contracts or shell completion hooks could break custom parameter behavior.
  Mitigation: Maintain comprehensive unit tests covering custom parameter conversion, normalization functions, and metavar output across framework updates.
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
- Encapsulate token normalization and case-folding logic within the convert method of the parameter type rather than dispersing it across command implementations.
- Ensure get_metavar distinguishes required and optional parameters through standard syntactic boundary delimiters.

## Continuation Context

Verify commands:

- Discover the project test runner and execute the test suite validating parameter type conversion and metavar generation.
- Discover the static analysis tool and verify that command definitions utilize registered parameter type subclasses without inline argument parsing.

Accept when:

- All parameter type verification tests pass, confirming expected value conversion, token normalization, and metavar formatting.
- Command-line help generation produces well-formatted parameter signatures without display wrapping degradation.

## Enforcement

- Verified by: Automated test suites validating option conversion, token normalization, and metavar generation.
- Verified by: Code review inspection of command parameter declarations.
- Violation handling: Pull requests introducing manual argument validation in command bodies or improper choice types are rejected during review.
- Exception process: Submit an architectural review request justifying why standard or custom parameter type protocols cannot support the input requirements.
