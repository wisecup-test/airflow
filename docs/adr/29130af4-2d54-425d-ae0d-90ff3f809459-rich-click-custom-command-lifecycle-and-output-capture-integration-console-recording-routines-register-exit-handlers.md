---
version: 2
name: 29130af4-2d54-425d-ae0d-90ff3f809459-rich-click-custom-command-lifecycle-and-output-capture-integration-console-recording-routines-register-exit-handlers
description: "rich_click Custom Command Lifecycle and Output Capture Integration: Console Recording Routines Register Exit Handlers"
metadata:
  actual:
    adr_id: 29130af4-2d54-425d-ae0d-90ff3f809459
    status: accepted
    candidate_group_id: 481381f5-0a91-4ff6-9905-f5278118e88e
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/utils/**
---

# rich_click Custom Command Lifecycle and Output Capture Integration: Console Recording Routines Register Exit Handlers

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/utils/**`

## Context

- The project developer CLI requires uniform rich terminal rendering across numerous subcommands and options.
- Automated diagnostic reproduction instructions must be consistently emitted when commands fail during automated test runs.
- Terminal help output requires automated capture as visual artifacts for documentation without altering command definitions.
- Standard CLI primitives lack built-in hooks for centralized post-execution reporting and automated console export.

## Problem Statement

Standard CLI command handlers execute business logic without centralized hooks for execution diagnosis or rich console capture. Implementing diagnostic output and documentation recording within individual commands leads to code duplication, inconsistent terminal output, and missing reproduction information during continuous integration failures.

## Decision

- **R-LM-001** SHOULD: Console recording routines SHOULD register exit handlers through system lifecycle callbacks during recording initialization.

## Policy Block

In scope:

- Development CLI command definitions and group registries.
- Terminal help text formatting and documentation recording utilities.

Out of scope:

- Core runtime application execution outside developer tooling.
- Standard third-party CLI subcommands that do not inherit from internal tooling groups.

## Rationale

- Subclassing rich_click command and group abstractions centralizes invocation interception and output styling across the entire CLI hierarchy.
- Wrapping the invoke lifecycle method inside structured error handling guarantees that diagnostic reproduction advice executes regardless of whether the command succeeded or failed.
- Separating console recording into dedicated utility functions registered with exit callbacks decouples terminal image generation from command logic.

## Consequences

Positive:

- Centralized lifecycle hooks guarantee consistent reproduction instructions for all CLI commands upon failure.
- Visual formatting and terminal recording configurations are decoupled from individual command implementations.
- Command groups automatically propagate customized command classes across nested subcommand trees.

Negative:

- Subclassing framework command and group primitives creates tight coupling to rich_click internal class hierarchies.
- Global console interception and exit hook registration introduce implicit runtime side effects across the terminal environment.

## Alternatives

- Decorate every individual command function with diagnostic and recording decorators (rejected)
  Rejected because: Requires manual application across every command definition, introducing high risk of omission and inconsistent lifecycle enforcement.
  When valid: Valid when command structures are strictly heterogeneous and do not share a common root group.
- Adopt standard click primitives without rich_click extensions (rejected)
  Rejected because: Lacks built-in rich terminal formatting and requires extensive custom console capture logic across all subcommands.
  When valid: Valid for lightweight CLI utilities with minimal output styling requirements.

## Risks

- Upstream changes in rich_click internal command or group signatures could break custom lifecycle overrides.
  Mitigation: Strictly verify the resolved version against the authoritative lock artifact and maintain automated test coverage of command invocation.
  Owner: engineering team
- Exit callbacks registered for output generation could hang or silently fail during unexpected process termination.
  Mitigation: Implement defensive exception handling around recording consoles and ensure graceful fallback when recording environment variables are absent.
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
- Derive custom command and group classes by subclassing framework base classes, ensuring the invoke override executes the superclass implementation before invoking diagnostic reproduction routines.
- Guard all console recording mechanisms with environment configuration checks so that recording consoles only instantiate when designated recording variables are present.

## Continuation Context

Verify commands:

- Discover and execute the project CLI test suite to verify that custom command lifecycle and reproduction hooks execute cleanly.
- Discover and run the terminal output recording verification routine to ensure output generation produces valid artifacts.

Accept when:

- All custom CLI commands and groups successfully invoke their delegated actions while executing lifecycle reproduction hooks.
- Output recording cleanly captures formatted console content without altering standard command exit codes.

## Enforcement

- Verified by: Automated test suites validating custom command invocation and error handling.
- Verified by: Code review checks ensuring new CLI commands inherit from custom command groups rather than standard base primitives.
- Violation handling: Pull requests introducing direct standard click command instantiations or bypassing group abstractions will be blocked until refactored.
- Exception process: Exceptions require approval from the tooling maintainers with documented justification explaining why custom command lifecycle interception cannot be used.
