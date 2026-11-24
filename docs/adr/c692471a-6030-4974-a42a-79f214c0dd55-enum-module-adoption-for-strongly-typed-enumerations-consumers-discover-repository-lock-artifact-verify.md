---
version: 2
name: c692471a-6030-4974-a42a-79f214c0dd55-enum-module-adoption-for-strongly-typed-enumerations-consumers-discover-repository-lock-artifact-verify
description: "enum Module Adoption for Strongly-Typed Enumerations: Consumers Discover Repository Lock Artifact Verify"
metadata:
  actual:
    adr_id: c692471a-6030-4974-a42a-79f214c0dd55
    status: accepted
    candidate_group_id: 00934b03-7054-425c-a97f-e7ecbe4ac60e
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/api/**
---

# enum Module Adoption for Strongly-Typed Enumerations: Consumers Discover Repository Lock Artifact Verify

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `src/airflowctl/api/**`

## Context

- API client configurations and data models require bounded sets of values to represent operational modes and payload categories.
- Passing unstructured string primitives across client transport layers introduces risks of runtime typographical errors and invalid execution states.
- Static analysis detects consistent subclassing of enum.Enum to establish typed enumeration members for client modes and data model representations.

## Problem Statement

Managing discrete client operational modes and data model representations using unstructured string primitives leads to silent runtime failures, typos, and lack of compile-time verification across client and server boundaries.

## Decision

- **R-LM-001** MUST: Consumers MUST discover the repository lock artifact and verify the exact runtime language and environment resolution before implementing or extending enumeration definitions.

## Policy Block

In scope:

- Client boundary definitions and public request dispatchers requiring fixed execution modes
- Data transfer models and contract specifications defining bounded sets of categorical values

Out of scope:

- Dynamic runtime collections where variant values are determined from external database records
- Open-ended configuration values and arbitrary user inputs with unbounded cardinalities

## Rationale

- Subclassing enum.Enum provides distinct symbolic types that prevent accidental assignment of invalid literal values across client boundaries.
- Centralizing discrete modes in enumeration definitions enables static analysis to catch unrecognized variants prior to runtime execution.
- Explicit string member assignments guarantee deterministic serialization formats when exchanging data across external client contracts.

## Consequences

Positive:

- Prevents typos and invalid state values by enforcing type-checked discrete variants across client operations.
- Improves discoverability and IDE autocompletion for supported execution modes and bounded domain options.
- Standardizes serialization behavior between client transport layers and backend API contracts.

Negative:

- Requires explicit member resolution when transforming data between raw network payloads and internal structures.
- Adds slight verbosity compared to using raw primitive strings directly in function signatures.
- Extending existing enumerations with new members requires updating all exhaustive pattern matches across consuming code.

## Alternatives

- Literal String Types and Raw Constants (rejected)
  Rejected because: Raw strings lack runtime namespace encapsulation and do not provide unified iteration or reverse lookup mechanisms across module boundaries.
  When valid: Lightweight internal helper functions where defining dedicated enumeration classes introduces unnecessary boilerplate.
- Integer Flags and Bitmasks (rejected)
  Rejected because: Integer representations obscure human readability in API serialization payloads and logs, increasing debugging complexity.
  When valid: High-throughput binary protocols requiring compact bitwise packing and low memory footprints.

## Risks

- Deserialization errors occurring when an external service returns an unrecognized enumeration variant.
  Mitigation: Implement defensive deserialization fallbacks or schema validation hooks that log unmapped variants gracefully.
  Owner: API Client Engineering Team
- Accidental drift between enumeration member values and upstream API contract specifications.
  Mitigation: Automate contract schema verification against API specifications within the test pipeline.
  Owner: API Client Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Define enumeration classes in designated contract and client configuration modules to ensure consistent reusability across components.
- When mapping incoming API payload values to enumeration instances, use constructor lookups inside safe boundary adapters to isolate invalid payload inputs.

## Continuation Context

Verify commands:

- Discover and execute the repository test suite to verify that all enumeration classes instantiate with expected member values.
- Discover and run the project static type checker to confirm exhaustive handling and correct type annotations across all enumeration usages.
- Discover and run the project linter and contract verification routines to validate that serialized enumeration members match schema expectations.

Accept when:

- All enumeration member definitions resolve to valid values and pass automated unit tests without regression.
- The project static type verification completes with zero type errors related to enumeration assignments.
- Network serialization and deserialization cycles preserve enumeration value fidelity across client boundaries.

## Enforcement

- Verified by: Automated static type analysis executed during repository integration pipelines.
- Verified by: Peer code review verifying that new discrete states subclass enum.Enum rather than using loose string constants.
- Violation handling: Pull requests introducing loose primitive strings for bounded modes will be blocked until refactored into enumeration members.
- Violation handling: Static type analysis failures triggered by invalid enumeration values halt pipeline progression.
- Exception process: Submit an architectural review request detailing why dynamic strings or loose constants are required over structured enumerations.
- Exception process: Exception requests must be approved by the client architecture maintainers and documented in the repository review record.
