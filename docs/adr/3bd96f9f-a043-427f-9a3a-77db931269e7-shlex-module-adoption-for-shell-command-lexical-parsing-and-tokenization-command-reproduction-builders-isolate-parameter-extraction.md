---
version: 2
name: 3bd96f9f-a043-427f-9a3a-77db931269e7-shlex-module-adoption-for-shell-command-lexical-parsing-and-tokenization-command-reproduction-builders-isolate-parameter-extraction
description: "shlex Module Adoption for Shell Command Lexical Parsing and Tokenization: Command Reproduction Builders Isolate Parameter Extraction"
metadata:
  actual:
    adr_id: 3bd96f9f-a043-427f-9a3a-77db931269e7
    status: accepted
    candidate_group_id: b5e85b92-616b-4ef8-a94b-03413a178942
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# shlex Module Adoption for Shell Command Lexical Parsing and Tokenization: Command Reproduction Builders Isolate Parameter Extraction

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/**`

## Context

- Developer tooling commands and reproduction utilities compose, tokenize, and execute shell command strings across varied environments.
- Naive string splitting or unquoted argument concatenation introduces parsing errors when parameters contain whitespace, quotes, or environment variables.
- Consistent lexical parsing across command runners and CI reproduction builders is necessary to ensure local commands reproduce remote execution deterministically.

## Problem Statement

Subprocess execution and command reproduction logic frequently handle composite command strings, flags, and environment parameters across different platform environments. Unstructured string splitting or manual quoting leads to shell injection risks, broken argument boundaries, and divergence between interactive invocation and automated script execution. The architecture requires a standardized, deterministic approach to command tokenization and lexical parsing.

## Decision

- **R-LM-001** SHOULD: Command reproduction builders SHOULD isolate parameter extraction from argument formatting by normalizing command tokens into structured collections before lexical assembly.

## Policy Block

In scope:

- Developer tooling utilities responsible for building, reproducing, or executing command-line invocations.
- Modules handling command string tokenization and argument escaping.

Out of scope:

- Direct program invocations passing pre-tokenized argument vectors directly to execution runtimes without shell parsing.
- In-memory data transformations independent of command generation.

Exceptions:

- **EXC-20-001** Direct argument arrays are supplied without shell interpretation requirements.
  Approval: Architecture Review Board
  Documentation: Document rationale in the module header explaining why shell lexical parsing is bypassed.

## Rationale

- The shlex module provides predictable lexical analysis, preventing malformed argument boundaries when parameters contain spaces or special characters.
- Evidence across command runners and CI reproduction utilities demonstrates the necessity of uniform tokenization to ensure local execution reproduces remote workflows identically.
- Eliminating manual string splitting mitigates command injection hazards and platform-specific path separator anomalies across developer execution environments.

## Consequences

Positive:

- Eliminates argument boundary corruption caused by spaces and special characters in command parameters.
- Mitigates command injection vulnerabilities across process invocation boundaries.
- Guarantees consistent command tokenization between local interactive execution and automated execution environments.

Negative:

- Introduces minor computational overhead during lexical analysis of large command strings.
- Requires developers to consistently route arguments through structured tokenization rather than direct string formatting.

## Alternatives

- Manual string concatenation and naive whitespace splitting (rejected)
  Rejected because: Fails on arguments containing whitespace or shell metacharacters, leading to malformed execution and potential command injection vulnerabilities.
  When valid: Never valid in production or developer command execution pipelines.
- External third-party command parsing libraries (rejected)
  Rejected because: Introduces redundant external dependency overhead when core library lexical parsing satisfies all command tokenization and escaping requirements.
  When valid: When advanced grammar parsing beyond standard shell lexical syntax is explicitly mandated.

## Risks

- Inconsistent handling of non-standard shell quoting behaviors across heterogeneous host platforms.
  Mitigation: Ensure lexical analysis parameters explicitly specify standard compliance mode across execution utilities.
  Owner: Developer Tooling Team
- Developers bypassing lexical escaping in newly added utility functions.
  Mitigation: Enforce automated static analysis checks and code review gates on all command-building functions.
  Owner: Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Inspect repository command execution helper modules to ensure all new subprocess invocation routines delegate argument tokenization to lexical analysis functions.
- Verify that command reproduction routines apply lexical quoting before formatting command strings for console or script output.

## Continuation Context

Verify commands:

- Discover the project test runner and execute unit tests targeting developer command utilities and argument tokenization modules.
- Discover the repository static analysis linter and run static checks across tooling packages to verify proper lexical parsing usage.

Accept when:

- All unit tests covering command construction, reproduction, and execution pass with zero failures.
- Static analysis validates that no unescaped string concatenation or manual whitespace splitting occurs in command composition modules.

## Enforcement

- Verified by: Automated test suites validating command construction and lexical tokenization behavior.
- Verified by: Peer code review verification on all pull requests modifying command composition utilities.
- Violation handling: Automated test failures block integration of code introducing unescaped command composition.
- Violation handling: Pull requests containing manual whitespace splitting or raw string concatenation for shell arguments must be revised before merge.
- Exception process: Submit an architectural exception request documenting why shell lexical parsing is inapplicable for the specific command pipeline, subject to maintainer approval.
