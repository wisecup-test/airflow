---
version: 2
name: 4aedb7d8-5480-4f6d-b26f-9c04c0342ae0-standard-library-json-and-tomllib-deserialization-for-input-parsing-components-parsing-structured-formats-utilize-strict
description: "Standard Library json and tomllib Deserialization for Input Parsing: Components Parsing Structured Formats Utilize Strict"
metadata:
  actual:
    adr_id: 4aedb7d8-5480-4f6d-b26f-9c04c0342ae0
    status: accepted
    candidate_group_id: 6f654889-96c1-4479-a7fe-0bd5404196ca
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
      - registry/**
      - stats/**
---

# Standard Library json and tomllib Deserialization for Input Parsing: Components Parsing Structured Formats Utilize Strict

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/**`
- `registry/**`
- `stats/**`

## Context

- Repository automation utilities and metadata generation routines require loading structured configuration data from disk and external service responses.
- Standard library modules json and tomllib provide zero-dependency deserialization for structured payloads across build and maintenance scripts.
- Contract validation using dedicated schema models exists in isolated data boundaries, whereas utility workflows parse raw inputs directly without formal schema guarantees.
- Unconstrained deserialization of untrusted or unvalidated artifacts introduces risks of attribute errors, unexpected data structures, or security vulnerabilities.

## Problem Statement

Development tooling and registry utilities ingest structured configuration files and API responses using direct calls to standard library parsers without universal schema validation. While contract models enforce strict attribute structures at specific catalog boundaries, utility scripts predominantly rely on unvalidated dictionary lookups, leaving automated tasks susceptible to schema drift, unhandled key errors, and unverified inputs.

## Decision

- **R-SCP-001** SHOULD: Components parsing structured formats SHOULD utilize strict schema configuration options that forbid undeclared attributes when enforcing data contracts.

## Policy Block

In scope:

- All utility scripts, build tools, and metadata generators that ingest structured file formats or remote API payloads.

Out of scope:

- Internal unit test fixtures and static mocks where input structure is defined entirely within test scope.

Exceptions:

- **EXC-42-001** A developer tool executes in an isolated environment against immutable repository-committed configuration files without accepting external inputs.
  Approval: Architecture Review Board or Security Team
  Documentation: Record the unvalidated input justification in the script header documentation.

## Rationale

- Direct standard library parsing minimizes build-time dependency overhead for lightweight operational and administrative tasks.
- Relying solely on implicit dictionary structures across scripts leads to unhandled runtime errors when schemas evolve across repository revisions.
- Enforcing explicit contract validation at external data boundaries isolates downstream processing from malformed or unexpected data shapes.

## Consequences

Positive:

- Explicit data contracts prevent runtime failures caused by missing or unexpected configuration keys.
- Standardized validation prevents malformed external payloads from corrupting generated metadata catalogs.
- Adherence to safe deserialization eliminates arbitrary code execution vectors associated with unsafe object deserializers.

Negative:

- Defining and maintaining explicit contract schemas adds boilerplate to small automation scripts.
- Stricter parsing requirements may cause legacy configuration files with unexpected fields to fail until schemas are updated.

## Alternatives

- Mandate comprehensive third-party model contract validation across all utility and maintenance scripts (rejected)
  Rejected because: Imposing heavy external validation dependencies on lightweight standalone administrative tasks creates unnecessary maintenance overhead and complicates dependency bootstrapping.
  When valid: When building public-facing services, untrusted input boundaries, or distributable metadata registries.
- Permit unconstrained ad hoc dictionary parsing across all internal and registry tooling (rejected)
  Rejected because: Unchecked dictionary traversal leads to silent schema corruption, lack of type safety, and failure to detect malformed data early in automation workflows.
  When valid: Never for production-bound metadata generation or security-sensitive boundaries.

## Risks

- Unsafe deserialization primitives inadvertently introduced in auxiliary scripts can execute arbitrary code when parsing untrusted inputs.
  Mitigation: Enforce static analysis rules and code review policies that prohibit unsafe deserialization functions in favor of safe structured parsers.
  Owner: Engineering Team
- Overly restrictive schema validation rejecting unexpected but harmless fields in evolving configuration files.
  Mitigation: Allow versioned contract models with documented migration paths and deprecation cycles for modified schemas.
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
- Inspect the project dependency configuration to identify existing contract modeling libraries before introducing validation dependencies into tooling.
- When defining data models for deserialized payloads, configure strict schema settings that reject extra fields on external data boundaries to ensure structural integrity.

## Continuation Context

Verify commands:

- Discover the repository static analysis suite and execute verification against structured input parsing routines.
- Discover and run the project test runner to validate that data deserialization contracts enforce input integrity.

Accept when:

- Verification suite succeeds with zero schema validation failures across structured data loaders.
- All external data deserialization entry points enforce strict typing and reject malformed payloads.

## Enforcement

- Verified by: Continuous integration static analysis checks scanning for unsafe deserialization functions.
- Verified by: Automated test suites executing schema validation against sample configuration files and API responses.
- Verified by: Peer code review verifying that new data ingestion endpoints incorporate appropriate validation boundaries.
- Violation handling: Automated static checks fail builds that invoke disallowed deserialization functions.
- Violation handling: Pull requests lacking contract validation for public-facing data ingestion are blocked from merging until compliant schemas are supplied.
- Exception process: Submit an architectural exception request detailing why strict schema validation is infeasible for the specific script, subject to review and sign-off by the security team.
