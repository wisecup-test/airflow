---
version: 2
name: ad4898a9-5430-409b-8a7e-d19f5b035c07-rich-console-subclassing-via-airflowconsole-for-cli-output-protocols-command-implementations-not-emit-unformatted-text
description: "Rich Console Subclassing via AirflowConsole for CLI Output Protocols: Command Implementations Not Emit Unformatted Text"
metadata:
  actual:
    adr_id: ad4898a9-5430-409b-8a7e-d19f5b035c07
    status: accepted
    candidate_group_id: 40a1c0b8-dccb-4b31-8107-a44b5e4e5462
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - docs/**
      - src/airflowctl/**
---

# Rich Console Subclassing via AirflowConsole for CLI Output Protocols: Command Implementations Not Emit Unformatted Text

Date: 2025-02-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `docs/**`
- `src/airflowctl/**`

## Context

- The command-line interface serves as both an interactive user terminal interface and a scriptable public protocol for downstream automation workflows.
- Without a centralized console protocol, disparate subcommands implement bespoke output rendering and print routines, resulting in inconsistent formatting and fragile shell pipelines.
- Terminal streams redirected to non-interactive processes require fixed column boundaries to prevent truncated output and malformed serialization.
- Subclassing the third-party Rich Console allows the project to enforce consistent output format dispatching across all operational subcommands.

## Problem Statement

Direct invocation of ad-hoc terminal print statements across diverse CLI subcommands creates inconsistent user experiences, introduces fragile parsing for downstream pipelines, and fails to handle non-interactive terminal redirection reliably. A unified console protocol is required to standardize output formatting across human-readable tables and machine-readable data streams while centralizing terminal capability detection.

## Decision

- **R-PEA-001** MUST_NOT: Command implementations MUST NOT emit unformatted text or bypass AirflowConsole for operational success or error messages intended for consumption by terminal users or automated scripts.

## Policy Block

In scope:

- All command-line subcommands emitting informational or structured data responses to stdout or stderr.
- Terminal formatting and presentation utility modules within the command-line interface layer.

Out of scope:

- Internal library helper functions and data manipulation routines that do not generate user-facing console output.
- Low-level network client transport mechanisms communicating directly over network protocols.

Exceptions:

- **EXC-25-001** A subcommand requires raw binary streaming or specialized interactive progress elements provided directly by lower-level terminal components.
  Approval: Architecture Review Board
  Documentation: Document exception in the subcommand design specification with explicit terminal capability verification.

## Rationale

- Subclassing Rich Console centralizes terminal stream configuration, ensuring that non-interactive pipelines receive deterministic width boundaries without line wrapping corruption.
- Abstracting format dispatching behind a unified protocol enables seamless switching between structured serialization and human-readable tables across all subcommands.
- Decoupling data retrieval from presentation formatting allows command handlers to remain focused on operational orchestration rather than output rendering mechanics.

## Consequences

Positive:

- Provides deterministic, pipe-safe output formatting across all command-line operations.
- Standardizes multi-format serialization capabilities across subcommands without duplicating presentation logic.
- Centralizes terminal environment detection and width negotiation in a single presentation component.

Negative:

- Introduces an abstraction layer that developers must adhere to when introducing new command-line outputs.
- Requires structured payloads to be normalized into dictionary or sequence representations compatible with tabular rendering.

## Alternatives

- Direct Standard Library Printing (rejected)
  Rejected because: Lacks native support for multi-format serialization, tabular rendering, and automated terminal width detection, leading to duplicated formatting logic across subcommands.
  When valid: Valid only in standalone scripts with zero external dependencies and no multi-format presentation requirements.
- Unmodified Direct Invocations of Third-Party Console Methods (rejected)
  Rejected because: Fails to enforce uniform width constraints for non-interactive streams and does not provide standardized routing across structured output formats.
  When valid: Valid when building exploratory prototypes that do not require integration into automated shell pipelines.

## Risks

- Developers might bypass the specialized console protocol and use direct standard print calls in new subcommands.
  Mitigation: Enforce static analysis rules and peer code review to prevent unapproved print statements across the command package.
  Owner: CLI Architecture Team
- Complex nested data models may fail tabular serialization if normalization routines are incomplete.
  Mitigation: Implement comprehensive unit test fixtures validating normalization behavior across all registered data schemas.
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
- Concrete format rendering implementations should isolate format-specific syntax highlighting and wrapping behavior behind dedicated handler methods within the console subclass.
- Normalization routines must recursively transform nested collections into flattened delimited strings when rendering tables while preserving native dictionary structures for serialized outputs.

## Continuation Context

Verify commands:

- Discover and execute the project unit and integration test suite to verify console output formatting and command rendering protocols.
- Discover and execute the project linter and static type analysis scripts to validate compliance with console subclassing signatures.

Accept when:

- All automated test suites for command-line output formatting pass without regression.
- Static typing checks verify that all commands outputting structured collections interact through the centralized console abstraction.
- Output redirection in non-interactive shell streams maintains consistent column alignment and valid syntax without stream corruption.

## Enforcement

- Verified by: Automated static analysis checks scanning command implementations for direct standard output invocations.
- Verified by: Comprehensive test suites verifying multi-format rendering across all CLI subcommands.
- Verified by: Peer code review for all pull requests adding or modifying command presentation layers.
- Violation handling: Pull requests containing unapproved direct print statements or bypassing the console protocol will be rejected by continuous integration gates.
- Violation handling: Violations discovered in the codebase must be refactored to use the centralized console abstraction before release.
- Exception process: Submit an architectural exception request detailing the technical necessity for bypassing the console protocol to the Architecture Review Board.
- Exception process: Approved exceptions must be tracked with an expiration date and recorded in the architectural documentation repository.
