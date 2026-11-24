---
version: 2
name: e1a95bda-c3eb-45d9-8d4b-a0409e2a6f7f-argparse-cli-command-definition-and-subparser-architecture-subcommand-parsers-requiring-custom-help-formatting
description: "argparse CLI Command Definition and Subparser Architecture: Subcommand Parsers Requiring Custom Help Formatting"
metadata:
  actual:
    adr_id: e1a95bda-c3eb-45d9-8d4b-a0409e2a6f7f
    status: accepted
    candidate_group_id: 78f62221-87d2-4bee-9125-a90fc25fc3ac
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/ctl/**
---

# argparse CLI Command Definition and Subparser Architecture: Subcommand Parsers Requiring Custom Help Formatting

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `src/airflowctl/ctl/**`

## Context

- The CLI subsystem requires a structured, declarative mechanism for defining command hierarchies, options, and subcommand groups without introducing external heavy runtime parsing frameworks.
- Command-line interfaces spanning complex operations suffer from degraded startup latency if all subcommand handlers and dependencies are imported eagerly during parser construction.
- Help output across diverse command groups requires clear visual separation between standalone commands and nested subcommands, necessitating custom subparser action formatting.
- Argument help strings and command entrypoints benefit from deferred resolution to avoid early initialization costs until a specific command path is executed.

## Problem Statement

Command-line subsystems in modular applications face a tension between comprehensive argument definitions and fast invocation latency. Eagerly loading every subcommand handler and help definition during parser generation degrades responsiveness, while standard parser output lacks structured visual grouping for complex subcommand sets. A standardized approach is required to construct declarative command-line parsers using the standard argument parsing library, deferring handler and help resolution until execution.

## Decision

- **R-LM-001** MUST: Subcommand parsers requiring custom help formatting MUST subclass parser formatter classes and override subaction iteration to separate command groups from individual subcommands.

## Policy Block

In scope:

- Command-line interface parser construction and argument configuration.
- Subcommand registration and dispatch routing within CLI modules.
- Custom help message formatters and argument resolution proxies.

Out of scope:

- HTTP API request parsing and route handling.
- Internal configuration loading from file sources or environment variables.
- Direct invocation of domain logic outside CLI execution pathways.

Exceptions:

- **EXC-20-001** A lightweight administrative command requires immediate execution without modular subparser grouping or lazy proxying overhead.
  Approval: Architecture Review Board
  Documentation: Document rationale in the command definition docstring with startup benchmark comparisons.

## Rationale

- Standardizing on argparse ensures consistent command definition semantics across CLI configuration and parser modules without external parsing framework overhead.
- Employing lazy command loading and proxy evaluation decouples parser creation from heavy downstream dependencies, preserving rapid CLI startup times.
- Customizing help formatters to partition subparser actions into distinct command groups and actions enhances user discoverability in complex CLI hierarchies.

## Consequences

Positive:

- Decouples command tree declaration from command handler execution, minimizing startup latency across all CLI invocations.
- Standardizes argument definition, subparser routing, and error formatting across all command-line operations.
- Enables structured help output that visually separates high-level command groups from atomic subcommands.

Negative:

- Requires custom formatter and action sub-classing that depends on internal subparser action structures.
- Introduces deferred resolution indirection, making tracing from parser definition to handler implementation less direct.
- Requires strict isolation to prevent accidental eager imports during parser building.

## Alternatives

- Eager command loading with direct function references in argument parser definitions (rejected)
  Rejected because: Forces full import of domain libraries and external dependencies during parser construction, introducing severe startup latency for every CLI invocation.
  When valid: Small monolithic command-line utilities with minimal dependency trees and trivial startup times.
- Third-party CLI declaration framework replacing the standard argument parsing library (rejected)
  Rejected because: Introduces additional external runtime dependencies and potential version conflicts across deployment environments.
  When valid: Applications requiring advanced interactive terminal prompting or complex shell completion features not supported by standard parsers.

## Risks

- Custom help formatters accessing private subparser structures may break across underlying parser runtime updates.
  Mitigation: Encapsulate subparser partitioning in dedicated formatter classes covered by comprehensive parser formatting unit tests.
  Owner: CLI Engineering Team
- Developers may inadvertently import domain logic or heavy clients inside parser definition modules, re-introducing startup latency.
  Mitigation: Enforce lazy resolution contracts through static boundary linting and import timing checks in automated testing pipelines.
  Owner: CLI Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Construct command definitions using modular parser builder functions that accept parser instances and register arguments without executing commands.
- Implement dynamic string-based module resolution functions to load and invoke target command handlers only after argument validation succeeds.
- Ensure lazy proxy objects wrapping argument help strings implement string coercion methods compatible with custom rich formatters.

## Continuation Context

Verify commands:

- Discover and execute the project test runner script targeting CLI parser configuration and command routing test suites.
- Discover and execute the repository static analysis and formatting check script to verify parser interface compliance.
- Discover and execute the CLI startup latency verification script to ensure deferred command loading operates within expected bounds.

Accept when:

- All CLI parser configuration test suites pass without errors or deprecation warnings.
- Parser construction completes without triggering imports of domain execution logic.
- Help formatters display command groups and subcommands in separate formatted sections.

## Enforcement

- Verified by: Automated continuous integration checks executing parser test suites.
- Verified by: Peer code review for any changes adding or modifying CLI commands and parser configurations.
- Verified by: Import timing profiling to detect accidental eager dependency loading during parser construction.
- Violation handling: Pull requests introducing eager command imports or improper subparser configurations will be blocked from merging.
- Violation handling: Violations identified post-merge must be remediated in an immediate follow-up patch.
- Exception process: Exceptions must be requested via an architectural review ticket describing the specific command requirements and latency impact.
- Exception process: Approval requires review and sign-off by the CLI subsystem maintainers.
