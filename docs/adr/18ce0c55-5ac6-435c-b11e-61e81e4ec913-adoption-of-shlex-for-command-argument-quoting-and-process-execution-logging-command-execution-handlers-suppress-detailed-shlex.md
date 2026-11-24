---
version: 2
name: 18ce0c55-5ac6-435c-b11e-61e81e4ec913-adoption-of-shlex-for-command-argument-quoting-and-process-execution-logging-command-execution-handlers-suppress-detailed-shlex
description: "Adoption of shlex for Command Argument Quoting and Process Execution Logging: Command Execution Handlers Suppress Detailed Shlex"
metadata:
  actual:
    adr_id: 18ce0c55-5ac6-435c-b11e-61e81e4ec913
    status: accepted
    candidate_group_id: 20470fc4-249d-4849-9005-87c1c833140c
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# Adoption of shlex for Command Argument Quoting and Process Execution Logging: Command Execution Handlers Suppress Detailed Shlex

Date: 2025-02-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/**`
- `src/tests_common/test_utils/**`

## Context

- External processes and build steps are orchestrated through shared development utilities and test helpers.
- Commands constructed as sequences of arguments require formatted string representations for diagnostics, execution logging, and error propagation.
- Unquoted or manually escaped argument strings risk misrepresenting arguments with whitespace or shell metacharacters during logging and execution.
- The shlex module provides standardized lexical analysis and POSIX-compliant argument quoting to safely represent command arguments.

## Problem Statement

Formatting command arguments for logging and subprocess execution without standardized quoting leads to ambiguous log outputs, difficult debugging of multi-word or special-character parameters, and potential shell injection vulnerabilities when strings are evaluated. The system requires a consistent, standard quoting mechanism across all command execution and build utility modules.

## Decision

- **R-LM-001** MAY: Command execution handlers MAY suppress detailed shlex.quote command logging in execution modes explicitly configured for silent execution.

## Policy Block

In scope:

- Command execution wrappers, test helpers, and build extensions that log or format process argument sequences.
- Diagnostic and error reporting components displaying subprocess command strings.

Out of scope:

- Subprocess execution where arguments are passed directly as arrays to operating system system calls without string logging.
- Internal string parsing unrelated to shell command construction or diagnostic output.

## Rationale

- Evidence across shared utilities demonstrates systematic adoption of shlex.quote across multiple execution contexts including build extensions and test command executors.
- Applying shlex.quote ensures that command strings logged for diagnostics can be copied directly into shell environments for faithful reproduction of execution behavior.
- Consistent argument sanitization prevents log injection and disambiguates arguments containing spaces or special characters in error diagnostics.

## Consequences

Positive:

- Diagnostic logs display faithful, reproducible representations of command invocations including complex arguments.
- Centralized quoting convention reduces boilerplate error formatting across test helpers and build extensions.
- Reduces risk of command ambiguity and accidental shell misinterpretation in log outputs and execution traces.

Negative:

- Additional processing overhead incurred from iterating and quoting every argument in large command sequences.
- Developers must ensure commands remain structured sequences until final logging or execution rather than relying on raw strings.

## Alternatives

- Raw string concatenation without escaping (rejected)
  Rejected because: Fails to distinguish individual arguments containing whitespace or shell metacharacters in diagnostics and causes unreproducible execution logs.
  When valid: Only acceptable for fixed single-token commands containing no user input, dynamic values, or whitespace.
- Custom regex-based escaping functions (rejected)
  Rejected because: Introduces maintenance overhead and subtle shell parsing discrepancies compared to POSIX-compliant standard library quotation.
  When valid: When targeting non-POSIX environments that require distinct, non-standard escaping semantics.

## Risks

- Inconsistent quotation if developers bypass the executor wrapper and format commands manually
  Mitigation: Channel all external process invocations through shared logging command executor abstractions.
  Owner: engineering team
- Accidental double-quoting if shlex.quote is applied multiple times to an argument string
  Mitigation: Apply quotation strictly at the boundary where argument sequences are converted to display strings.
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
- Transform command argument lists into display strings using generator expressions with shlex.quote immediately prior to log dispatch or error construction.
- Preserve command arguments as collections of strings throughout internal application workflows, deferring string conversion strictly to logging and presentation boundaries.

## Continuation Context

Verify commands:

- # Discover repository test runner from manifest and execute test suites targeting command execution utilities
- # Run repository static analysis to verify uniform usage of shlex.quote across command logging modules

Accept when:

- All test suites exercising command execution and logging wrappers execute with zero failures.
- Static analysis confirms all command string formatting routines invoke shlex.quote on argument elements.

## Enforcement

- Verified by: Automated test suites validating command executor logging and error message generation.
- Verified by: Code reviews checking that all external process logging utilizes standard argument quoting.
- Violation handling: Pull requests introducing unquoted command string formatting in logging or error handlers are blocked during review.
- Violation handling: Static analysis failures on unescaped command logging must be remediated prior to merge.
- Exception process: Exceptions for non-standard command formatting require documented approval from the architecture team detailing why standard POSIX quoting is unsuitable.
