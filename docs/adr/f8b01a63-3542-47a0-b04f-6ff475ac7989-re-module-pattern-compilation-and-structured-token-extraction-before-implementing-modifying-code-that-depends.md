---
version: 2
name: f8b01a63-3542-47a0-b04f-6ff475ac7989-re-module-pattern-compilation-and-structured-token-extraction-before-implementing-modifying-code-that-depends
description: "re Module Pattern Compilation and Structured Token Extraction: Before Implementing Modifying Code That Depends"
metadata:
  actual:
    adr_id: f8b01a63-3542-47a0-b04f-6ff475ac7989
    status: accepted
    candidate_group_id: f94cb5c1-0a66-42a1-bedc-df55a31c0772
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/dags/**
      - assign_cherry_picked_prs_with_milestone.py
      - breeze/src/airflow_breeze/**
      - chart/**
      - ide_setup/**
      - provider_db_inventory.py
      - react-plugin-tools/**
      - registry/**
      - stats/**
      - update_github_branch_config.py
---

# re Module Pattern Compilation and Structured Token Extraction: Before Implementing Modifying Code That Depends

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `airflow_perf/dags/**`
- `assign_cherry_picked_prs_with_milestone.py`
- `breeze/src/airflow_breeze/**`
- `chart/**`
- `ide_setup/**`
- `provider_db_inventory.py`
- `react-plugin-tools/**`
- `registry/**`
- `stats/**`
- `update_github_branch_config.py`

## Context

- Automated workflows, developer tooling, and metadata processing components routinely parse structured string expressions including version identifiers, pull request citations, configuration references, and temporal deltas.
- Ad-hoc procedural string operations such as nested string slicing and manual delimiter splitting produce fragile parsing logic that degrades maintainability when string formats evolve.
- The codebase establishes repeated adoption of the core regular expression module, standardizing pattern declaration and extraction across administrative, build, and runtime tooling modules.

## Problem Statement

String parsing and metadata extraction across tooling and operational scripts require a deterministic, maintainable approach that avoids brittle ad-hoc string slicing while avoiding heavyweight external parser dependencies. Without standardized regular expression usage conventions, varied string parsing implementations introduce inconsistencies, unhandled edge cases, and performance inefficiencies from repeated pattern recompilation.

## Decision

- **R-LM-001** MUST: Before implementing or modifying code that depends on runtime environment libraries, consumers MUST inspect the repository dependency manifest and lock artifact to verify the active runtime resolution.

## Policy Block

In scope:

- Developer tooling, build scripts, administrative utilities, and runtime modules performing text parsing, format validation, and structured metadata extraction.

Out of scope:

- Trivial fixed-string exact matching where standard constant string containment checks are sufficient.
- High-throughput data streaming pipelines where specialized binary serialization or dedicated parser generators are mandated.

Exceptions:

- **EXC-20-001** A high-performance critical path demonstrates measurable overhead from regular expression engine evaluation and requires specialized lexical tokenization.
  Approval: Architecture Review Board
  Documentation: Benchmark analysis documenting latency differentials and parser correctness tests recorded in the module documentation.

## Rationale

- Standardizing on the core re module provides deterministic, zero-external-dependency string parsing across automation and utility modules.
- Pre-compiling pattern objects with re.compile avoids repeated internal pattern compilation overhead during iteration and standardizes regular expression definitions as top-level module constants.
- Named capture groups improve code clarity and maintainability by decoupling field extraction logic from positional capture indices.

## Consequences

Positive:

- Ensures consistent and predictable string validation and token extraction across disparate automation and operational utilities.
- Eliminates redundant regular expression pattern compilation overhead by standardizing on module-scoped compiled objects.
- Improves code readability and self-documentation by replacing opaque numerical indices with named capture groups.

Negative:

- Complex regular expressions can become difficult to debug and maintain if not accompanied by clear documentation and focused unit test suites.
- Improperly constructed regular expressions risk performance degradation under unexpected or pathological input variations.

## Alternatives

- Ad-hoc procedural string operations using chained manual delimiter splitting and substring slicing (rejected)
  Rejected because: Manual delimiter splitting is fragile when encountering edge cases, whitespace variations, and optional tokens, leading to brittle maintenance burdens.
  When valid: Valid only for trivial single-character delimiters with strictly invariant structural formats.
- External formal parser generation frameworks and third-party parsing libraries (rejected)
  Rejected because: External parser frameworks introduce heavy third-party dependency chains and unnecessary architectural complexity for tooling scripts.
  When valid: Valid when implementing full domain-specific programming languages or complex formal grammar compilers.

## Risks

- Unbounded execution latency resulting from exponential backtracking on non-matching pathological input strings.
  Mitigation: Enforce pattern reviews, constrain quantifier scopes, and validate regex structures against adversarial test cases.
  Owner: engineering team
- Silent parsing failures caused by unrecognized string format deviations when matching against unanchored patterns.
  Mitigation: Use explicit pattern anchors and enforce comprehensive test coverage for parsing functions.
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
- Declare compiled regular expression pattern objects as module-level constants with descriptive uppercase identifiers.
- Validate matched objects explicitly for non-null results before accessing capture groups or invoking match methods.

## Continuation Context

Verify commands:

- Discover the project test runner configuration from the repository and execute test suites targeting regular expression parsing utilities.
- Execute the repository code quality and static analysis checks to verify pattern compilation practices and compliance with lint rules.

Accept when:

- All unit and integration test suites covering regular expression extraction and parsing functions pass without failure.
- Static analysis checks confirm module-level pre-compilation of regular expression patterns and absence of lint violations.

## Enforcement

- Verified by: Automated continuous integration test suites and static analysis linters.
- Verified by: Peer code review verification during pull request evaluations.
- Violation handling: Automated continuous integration pipeline failures blocking pull request merges.
- Violation handling: Code review revision requests requiring conversion of ad-hoc string slicing or inline regex calls to compiled patterns.
- Exception process: Exceptions must be submitted via formal code review with benchmarking evidence demonstrating necessity and approved by project maintainers.
