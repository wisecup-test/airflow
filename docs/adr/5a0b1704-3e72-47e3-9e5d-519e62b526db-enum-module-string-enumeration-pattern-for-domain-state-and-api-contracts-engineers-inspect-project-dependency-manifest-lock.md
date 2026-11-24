---
version: 2
name: 5a0b1704-3e72-47e3-9e5d-519e62b526db-enum-module-string-enumeration-pattern-for-domain-state-and-api-contracts-engineers-inspect-project-dependency-manifest-lock
description: "Enum Module String Enumeration Pattern for Domain State and API Contracts: Engineers Inspect Project Dependency Manifest Lock"
metadata:
  actual:
    adr_id: 5a0b1704-3e72-47e3-9e5d-519e62b526db
    status: accepted
    candidate_group_id: c0fd51b0-0b7d-4ecc-927f-6d720c54729b
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Enum Module String Enumeration Pattern for Domain State and API Contracts: Engineers Inspect Project Dependency Manifest Lock

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Domain models, execution states, API request and response data contracts, and serialization protocols require consistent representations of discrete constant values across distributed boundaries.
- Unchecked primitive string literals across distributed services and persistence boundaries introduce susceptibility to silent typos, inconsistent serialization formats, and degraded schema introspection.
- Systems operating across relational persistence, dynamic serialization engines, and HTTP API endpoints require enumeration types that preserve primitive string semantics while providing strict type safety and membership validation.

## Problem Statement

Unconstrained string constants across domain entities, serialization pipelines, and API schemas cause runtime errors, schema drift, and brittle conditional logic. Domain state transitions and protocol markers must be constrained to explicit, inspectable definitions without breaking native string compatibility required by database columns and network serializers.

## Decision

- **R-LM-001** MUST: Engineers MUST inspect the project dependency manifest and lock artifact to verify the exact resolved runtime environment before implementing or updating enumeration modules.

## Policy Block

In scope:

- Declaration of domain model states, execution lifecycle statuses, and task trigger rules.
- FastAPI request and response schemas modeling categorical data.
- Serialization definitions and protocol markers exchanged across process boundaries.
- Authorization resource actions and authentication permission categories.

Out of scope:

- Arbitrary user-configurable labels or open-ended taxonomy tags.
- Continuous numerical ranges and dynamic configuration keys.

## Rationale

- Subclassing both the primitive string type and Enum guarantees backward compatibility with external serialization formats and relational database drivers while enforcing compile-time and static analysis checks.
- Centralizing categorical constants into formal enumeration classes eliminates typo-induced runtime errors and enables automated schema generation for API specifications.
- Inspecting enumeration member registries allows performant membership validation without instantiating ad-hoc lookup sets across request lifecycles.

## Consequences

Positive:

- Provides compile-time and static type safety across API boundaries, state machines, and database models.
- Enables native serialization to string format in JSON payloads and database tables without requiring custom serializer hooks.
- Simplifies schema generation and documentation for API client generators and public interfaces.

Negative:

- Requires defining and maintaining explicit enumeration classes for every categorical state space.
- Subtle behavior differences between raw strings and enumeration instances can emerge when interacting with third-party libraries that use strict type checking.

## Alternatives

- Raw string literals and untyped module-level string constants (rejected)
  Rejected because: Lacks static type safety, permits invalid string assignment without validation, and prevents automated API schema documentation.
  When valid: Small isolated script contexts where no public API contracts or complex state machines exist.
- Standard integer-based Enum classes without string inheritance (rejected)
  Rejected because: Requires explicit value unwrapping and custom serializers across relational persistence and JSON API boundaries.
  When valid: Internal binary protocol serialization where payload footprint takes precedence over human-readable inspection.
- Type union literals without runtime enumeration classes (rejected)
  Rejected because: Disappears at runtime, preventing runtime iteration, validation classmethods, and database mapping.
  When valid: Lightweight utility functions requiring purely compile-time typing with zero runtime overhead.

## Risks

- Developers might compare string enumerations against incompatible types or assume mutable instance attributes.
  Mitigation: Enforce strict static type checking in CI and establish linting rules preventing invalid comparisons.
  Owner: Engineering Team
- Adding new enumeration values may break external consumers expecting fixed exhaustive states if deserialization is strict.
  Mitigation: Design API schemas and state deserializers to handle forward-compatible unknown states gracefully.
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
- When defining string enumerations for API contracts, declare explicit string values for each member to prevent breaking protocol contracts during refactoring.
- Cache the result of member value collections when implementing high-frequency validation classmethods to avoid repeated dictionary traversals.

## Continuation Context

Verify commands:

- Discover the project's static type verification tool from repository configuration and run it across all modules declaring enumeration classes.
- Discover the test runner from the project manifest and execute the test suite covering domain state transitions and API contract serialization.
- Discover the codebase linter and verify that no raw string literals are used in place of defined enumeration members.

Accept when:

- Static type analysis passes without errors across all enumeration usages and API contract models.
- All unit and integration tests verifying state transitions, serialization, and deserialization execute successfully.
- No raw string literals are detected where domain state enumerations are defined.

## Enforcement

- Verified by: Automated static type checking in CI pipelines verifying enumeration assignments.
- Verified by: Peer code review for pull requests modifying domain states or API schemas.
- Violation handling: Pull requests containing untyped string literals for domain state or unvalidated constants must be rejected.
- Violation handling: CI static analysis failures block merge until enumeration types are properly adopted.
- Exception process: Exceptions require documented architecture approval and an explicit migration plan to typed enumerations.
