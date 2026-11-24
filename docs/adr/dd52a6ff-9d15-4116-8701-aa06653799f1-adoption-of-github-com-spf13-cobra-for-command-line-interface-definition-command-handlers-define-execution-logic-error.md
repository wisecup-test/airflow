---
version: 2
name: dd52a6ff-9d15-4116-8701-aa06653799f1-adoption-of-github-com-spf13-cobra-for-command-line-interface-definition-command-handlers-define-execution-logic-error
description: "Adoption of github.com/spf13/cobra for Command-Line Interface Definition: Command Handlers Define Execution Logic Error"
metadata:
  actual:
    adr_id: dd52a6ff-9d15-4116-8701-aa06653799f1
    status: accepted
    candidate_group_id: 3f6d8eed-96da-4b76-a75d-637735f622ff
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - cmd/airflow-go-pack/**
      - edge/commands/**
---

# Adoption of github.com/spf13/cobra for Command-Line Interface Definition: Command Handlers Define Execution Logic Error

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `cmd/airflow-go-pack/**`
- `edge/commands/**`

## Context

- The repository contains multiple executable tooling entrypoints and operational commands that require structured argument parsing and command dispatch.
- Standardizing on a single command-line interface library ensures consistent behavior, flag semantics, and lifecycle management across all tools.
- Codebase evidence demonstrates consistent adoption of github.com/spf13/cobra across multiple command packages and executables.

## Problem Statement

Without a consistent command-line framework, disparate tools and subcommands implement ad-hoc argument parsing, error handling, and flag management, leading to inconsistent user interfaces and increased maintenance overhead across operational tooling.

## Decision

- **R-LM-001** MUST: Command handlers MUST define execution logic using error-returning run functions to propagate execution failures to the runtime harness.

## Policy Block

In scope:

- All command-line interface entrypoints, root commands, and subcommands in the repository
- Operational tooling requiring argument parsing, command hierarchy, and execution dispatch

Out of scope:

- Core domain logic, data models, and service packages devoid of command-line interface interactions
- Shared algorithmic or utility packages with no command dispatch responsibilities

## Rationale

- Adopting github.com/spf13/cobra provides a battle-tested and extensible architecture for nested subcommands, automated documentation generation, and POSIX-compliant flag handling.
- A unified command framework simplifies developer onboarding and enables reuse of shared command configuration patterns across different tools.
- The observed pattern across multiple command packages demonstrates successful decoupling of root configuration from subcommand execution.

## Consequences

Positive:

- Uniform CLI behavior, flag parsing semantics, and help documentation across all tooling.
- Modular composition of root commands and subcommands across separate packages.
- Reduced boilerplate code for argument validation and command routing.

Negative:

- Command handlers incur direct structural coupling to the external command framework.
- Improper separation of concerns can lead to domain logic becoming entangled with command-line flag parsing.
- Upstream framework updates require coordinated verification across all dependent command packages.

## Alternatives

- Standard library argument parsing primitives (rejected)
  Rejected because: Standard library primitives lack built-in hierarchical subcommand trees, posix-compliant flag parsing, and automatic help generation.
  When valid: Valid for single-file utilities with flat argument structures and no nested subcommands.
- Multiple disparate third-party command-line libraries (rejected)
  Rejected because: Introducing competing command-line frameworks causes inconsistent flag semantics, divergent command hierarchies, and increased maintenance overhead.
  When valid: Valid when an isolated subsystem requires specialized interface paradigms that the primary framework cannot accommodate.

## Risks

- Coupling business logic directly to framework command handlers inhibits isolated testing.
  Mitigation: Isolate core operational business logic into domain packages and treat command handlers purely as adapters.
  Owner: engineering team
- Inconsistent flag naming or uncoordinated persistent state across deeply nested subcommands.
  Mitigation: Define shared root command hooks and centralized configuration objects to manage state cleanly.
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
- Separate command registration and flag binding from domain business logic by delegating core operational work to domain packages.
- Ensure root command structures establish shared persistent execution context and configuration state prior to dispatching subcommands.

## Continuation Context

Verify commands:

- # Discover repository dependency manifest and confirm resolved lockfile matches active environment
- # Discover and run repository static analysis to verify all command packages conform to framework conventions
- # Discover and run repository test suites validating CLI command dispatch and flag parsing

Accept when:

- All command-line interface entrypoints and subcommands route argument handling and execution exclusively through the adopted framework.
- All command handlers propagate execution errors to the runtime harness without unhandled panics.
- Repository test and static analysis verification suites pass across all command modules.

## Enforcement

- Verified by: Continuous integration pipelines executing repository test and static analysis suites
- Verified by: Peer code reviews validating adherence to command structure conventions and separation of concerns
- Violation handling: Pull requests introducing custom argument parsers or alternative command libraries are blocked until aligned with the standard.
- Violation handling: Existing non-conforming command definitions are flagged as architectural debt and scheduled for refactoring.
- Exception process: Exceptions require formal architectural review and consensus from repository maintainers before merging.
- Exception process: Approved exceptions must record technical justification and scope limitations in repository documentation.
