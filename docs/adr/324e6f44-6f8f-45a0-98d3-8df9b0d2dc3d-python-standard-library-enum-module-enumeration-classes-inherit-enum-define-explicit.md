---
version: 2
name: 324e6f44-6f8f-45a0-98d3-8df9b0d2dc3d-python-standard-library-enum-module-enumeration-classes-inherit-enum-define-explicit
description: "Python Standard Library enum Module: Enumeration Classes Inherit Enum Define Explicit"
metadata:
  actual:
    adr_id: 324e6f44-6f8f-45a0-98d3-8df9b0d2dc3d
    status: accepted
    candidate_group_id: afaa3a5c-5c91-4b30-acfe-aeb036e7cec4
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/dags/**
      - breeze/src/airflow_breeze/**
---

# Python Standard Library enum Module: Enumeration Classes Inherit Enum Define Explicit

Date: 2025-05-15
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `airflow_perf/dags/**`
- `breeze/src/airflow_breeze/**`

## Context

- The repository maintains developer tooling and test execution utilities that require distinct categorical constant domains.
- Unstructured string literals and raw numeric values across modules introduce risks of typographical errors and lack nominal type identities.
- The codebase incorporates the standard enumeration module to model discrete state spaces and domain classifications.

## Problem Statement

Unstructured constants and arbitrary string literals across development utilities and workload configurations reduce maintainability, obscure domain boundaries, and prevent static verification of categorical values. A structured approach is required to encapsulate finite value spaces into coherent, type-safe symbolic enumerations.

## Decision

- **R-LM-001** MUST: Enumeration classes MUST inherit from Enum and define explicit members for all permissible values within the domain.

## Policy Block

In scope:

- Categorical domain definitions across development tooling and script utilities.
- Shared constant collections representing finite, discrete states or attributes.

Out of scope:

- Dynamic user inputs and unbound open-ended text strings.
- High-throughput data serialization formats requiring raw primitive types without enumeration abstraction.

## Rationale

- Using the standard library enumeration module provides nominal type identity and namespace encapsulation without introducing third-party runtime dependencies.
- Symbolic enumerations prevent silent failures caused by misspelled string constants across disparate utility modules.
- The pattern relies exclusively on built-in standard library facilities, eliminating external supply-chain surface while standardizing domain definitions.

## Consequences

Positive:

- Provides centralized, nominal type definitions for categorical values across the repository.
- Reduces typographical errors and simplifies static analysis of allowed domain states.
- Imposes zero external dependency footprint by relying on standard library capabilities.

Negative:

- Requires lookup overhead and conversion handling when interfacing with external systems expecting primitive strings or integers.
- Adds syntactic ceremony compared to direct literal usage for small, single-use constants.

## Alternatives

- Plain String and Numeric Constants (rejected)
  Rejected because: Lacks namespace isolation, nominal typing, and validation guarantees across modules.
  When valid: Valid only in single-use scripts where categorical validation provides no maintenance benefit.
- Third-Party Enumeration and Validation Libraries (rejected)
  Rejected because: Introduces unnecessary external dependencies and maintenance overhead for functionality available in the standard library.
  When valid: Valid when complex serialization, validation schemas, or database object-relational mapping integrations require external validation libraries.

## Risks

- Serialization mismatch when external APIs or configuration stores expect primitive string representations.
  Mitigation: Implement explicit value extraction or string conversion at external client boundaries.
  Owner: engineering team
- Proliferation of redundant enumeration classes across independent utility modules.
  Mitigation: Consolidate domain enumerations into shared constant modules when used across multiple callers.
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
- Declare domain enumerations in dedicated constant or utility modules, exposing members as class attributes for consumption by client code.
- When mapping external inputs to enumeration members, handle potential lookup exceptions explicitly at the system boundary.

## Continuation Context

Verify commands:

- Discover the project static type checker and run verification across the codebase to ensure valid enumeration member usage.
- Discover the repository test runner and execute the test suite to confirm consistent enumeration evaluation.

Accept when:

- Static analysis validates that all enumeration references correspond to declared members without type errors.
- The project test suite executes successfully with zero failures originating from enumeration comparisons or lookups.

## Enforcement

- Verified by: Automated static analysis checks in continuous integration pipelines.
- Verified by: Peer review during code contribution workflows.
- Violation handling: Pull requests introducing loose literal strings for established enumeration domains are blocked until refactored.
- Violation handling: Static analysis type errors cause continuous integration build failure.
- Exception process: Exceptions require documented justification in the pull request detailing why a primitive representation is mandatory at an external interface.
