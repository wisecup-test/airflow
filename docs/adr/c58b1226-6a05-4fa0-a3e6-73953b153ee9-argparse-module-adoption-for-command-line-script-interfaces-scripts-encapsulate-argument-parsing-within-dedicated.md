---
version: 2
name: c58b1226-6a05-4fa0-a3e6-73953b153ee9-argparse-module-adoption-for-command-line-script-interfaces-scripts-encapsulate-argument-parsing-within-dedicated
description: "argparse Module Adoption for Command-Line Script Interfaces: Scripts Encapsulate Argument Parsing Within Dedicated"
metadata:
  actual:
    adr_id: c58b1226-6a05-4fa0-a3e6-73953b153ee9
    status: accepted
    candidate_group_id: 6082a516-84bd-4866-a226-b6d59946d385
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - ide_setup/**
      - prune_old_dirs.py
      - react-plugin-tools/**
      - registry/**
      - verify_release_calendar.py
---

# argparse Module Adoption for Command-Line Script Interfaces: Scripts Encapsulate Argument Parsing Within Dedicated

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `ide_setup/**`
- `prune_old_dirs.py`
- `react-plugin-tools/**`
- `registry/**`
- `verify_release_calendar.py`

## Context

- Developer tooling, data extraction routines, and registry maintenance scripts require command-line interfaces for local execution and automation pipelines.
- Across multiple automation scripts, command-line argument parsing is standardized using the argparse module rather than external packages or manual argument array indexing.
- Standardizing command-line argument handling reduces operational friction when executing maintenance tasks across the repository.

## Problem Statement

Operational scripts across developer tooling and data maintenance require robust, predictable command-line argument parsing without introducing external dependency coupling or inconsistent manual argument extraction.

## Decision

- **R-LM-001** SHOULD: Scripts SHOULD encapsulate argument parsing within dedicated functions and return structured argument namespaces to core processing routines.

## Policy Block

In scope:

- Standalone operational, maintenance, and automation scripts executed from command-line environments.
- Data extraction, registry synchronization, environment setup, and verification utilities within repository tooling.

Out of scope:

- Library modules imported solely as runtime components without executable entry points.
- Interactive user interfaces or web services requiring separate communication protocols.

Exceptions:

- **EXC-20-001** A specialized utility requires complex multi-level interactive prompts or terminal user interfaces not supported by standard argument parsing.
  Approval: Architecture review approval and documented rationale.
  Documentation: Documented within the script header explaining the necessity of an alternative interface protocol.

## Rationale

- Using argparse provides standardized flag parsing, type validation, and automatic help generation using built-in capabilities without adding third-party dependencies.
- Adoption across multiple automation utilities establishes a consistent developer experience and uniform error handling across the repository.

## Consequences

Positive:

- Eliminates external dependency requirements for developer tooling and operational scripts.
- Standardizes command-line flag conventions, automated help text generation, and exit behavior across scripts.
- Facilitates isolated unit testing of script routines by decoupling argument definition from execution logic.

Negative:

- Requires more parser configuration boilerplate compared to high-level declarative CLI frameworks.
- Lacks built-in support for complex terminal user interfaces or interactive prompts without supplemental logic.

## Alternatives

- Adopting external third-party command-line parsing frameworks (rejected)
  Rejected because: Introducing external dependencies for developer and maintenance utilities increases bootstrapping friction and introduces unnecessary supply-chain maintenance overhead.
  When valid: Valid for complex user-facing command-line tool suites requiring advanced shell completions and terminal formatting.
- Manual parsing of raw process argument vectors (rejected)
  Rejected because: Parsing raw argument lists leads to fragile flag parsing, inconsistent validation logic, and a lack of standard help output.
  When valid: Valid only in minimal bootstrap shims where process overhead must be near zero.

## Risks

- Verbose parser configuration boilerplate across multiple small utility scripts.
  Mitigation: Encapsulate recurring argument parsing patterns or helper functions within shared tooling modules.
  Owner: tooling and automation engineering team
- Inconsistent argument naming conventions across separate utility scripts.
  Mitigation: Establish project-wide conventions for flag names, option syntax, and exit code handling.
  Owner: tooling and automation engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Construct argument parser instances with explicit program descriptions and option help messages to preserve interface discoverability.
- Separate CLI argument parsing logic from domain processing logic so business operations can be tested independently of command-line invocation.

## Continuation Context

Verify commands:

- Discover and run the project static analysis suite to verify that command-line scripts import argparse and adhere to parser configuration conventions.
- Discover and execute the automated test runner targeting developer tooling and script test suites to validate argument parsing edge cases.

Accept when:

- All operational and automation scripts parse command-line options using argparse with structured help documentation and exit codes.
- Repository static analysis checks and test validation suites pass with zero violations regarding unparsed argument access.

## Enforcement

- Verified by: Automated static analysis checks integrated into repository continuous integration pipelines.
- Verified by: Peer code review during change evaluation for developer tooling and operational automation scripts.
- Violation handling: Changes introducing ad-hoc argument vector inspection or unapproved external CLI dependencies will be blocked during review until refactored to use argparse.
- Exception process: Exceptions require submission of an architectural review request detailing specific technical requirements that cannot be satisfied by the standard module.
