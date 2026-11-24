---
version: 2
name: 9f0442aa-890d-42fc-96ff-2472a9b7c98f-dataclasses-domain-and-parameter-model-architecture-derived-attributes-secondary-initializations-that-depend
description: "dataclasses Domain and Parameter Model Architecture: Derived Attributes Secondary Initializations That Depend"
metadata:
  actual:
    adr_id: 9f0442aa-890d-42fc-96ff-2472a9b7c98f
    status: accepted
    candidate_group_id: 7d219bf7-1bb5-440b-9598-6eb6541f4c38
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
      - registry/**
      - stats/**
      - verify_release_calendar.py
---

# dataclasses Domain and Parameter Model Architecture: Derived Attributes Secondary Initializations That Depend

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/**`
- `registry/**`
- `stats/**`
- `verify_release_calendar.py`

## Context

- Operational tooling and execution workflows require managing complex configuration options with shared defaults and environment overrides.
- Ad-hoc dictionaries and untyped parameter passing led to runtime attribute errors and opaque configuration contracts.
- The codebase establishes typed domain models by applying dataclass decorators to parameter classes and structuring common options via inheritance.
- Derived parameters and command flag synthesis require explicit lifecycle hooks and typed property accessors.

## Problem Statement

Managing multifaceted build and operational parameters through raw dictionary mappings or manual class initializers results in repetitive boilerplate, inconsistent type validation, and fragile configuration hierarchies across tooling subsystems.

## Decision

- **R-DM-001** SHOULD: Derived attributes or secondary initializations that depend on primary fields SHOULD be executed within the __post_init__ lifecycle hook.

## Policy Block

In scope:

- Domain entities, parameter definitions, and operational configuration state across tooling and operational modules.
- Hierarchical parameter models that generate command arguments, execution contexts, and artifact metadata.

Out of scope:

- Lightweight single-use data transfer tuples where attribute naming and typing are unnecessary.
- External data schemas governed by database ORM models or strict serialization schema libraries.

## Rationale

- The dataclasses module provides native declarative syntax for typed fields, eliminating verbose initialization logic across operational models.
- Base dataclass inheritance standardizes common flags and environment discovery logic across specialized parameter subclasses.
- The __post_init__ hook provides a structured lifecycle boundary for validating constraints and populating derived attributes without overriding constructors.

## Consequences

Positive:

- Declarative field typing and default value assignments reduce initialization boilerplate and runtime attribute errors.
- Inheritance hierarchies enable consistent reuse of shared configuration options across specialized parameter models.
- Lifecycle hooks and computed properties cleanly isolate domain transformations from consumer interfaces.

Negative:

- Subclassing dataclasses requires strict ordering of default and non-default fields across the inheritance hierarchy.
- Mutable dataclass attributes can introduce unintended side effects across calling contexts if immutability is not explicitly enforced.

## Alternatives

- Standard dictionary structures for domain state and parameter passing (rejected)
  Rejected because: Untyped dictionaries lack schema guarantees, auto-completion, and static type inspection, leading to runtime attribute errors.
  When valid: Acceptable only for transient key-value collections with arbitrary and unconstrained keys.
- Plain class definitions with manual constructor boilerplate (rejected)
  Rejected because: Requires repetitive field assignment, manual string representation methods, and error-prone equality implementations.
  When valid: Valid when custom metaclasses or low-level descriptor protocol mechanics supersede declarative attribute storage.

## Risks

- Field ordering conflicts in dataclass inheritance when parent classes define fields with default values.
  Mitigation: Structure inheritance trees to place default values consistently or utilize factory functions for optional fields.
  Owner: engineering team
- Accidental mutation of shared parameter instances across execution tasks.
  Mitigation: Utilize cloning operations or evaluate frozen dataclasses where read-only state is required.
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
- Ensure all fields declared in domain and parameter dataclasses specify explicit type annotations to enable static type analysis.
- Use the post-initialization hook for any field validation or dependent state assignment that relies on multiple declared attributes.

## Continuation Context

Verify commands:

- Discover the repository type verification runner from the project configuration and execute it across domain modeling modules.
- Discover and run the project test suite covering parameter models and domain entities to ensure attribute resolution passes.

Accept when:

- All domain and parameter dataclasses pass static type inspection with zero unresolved type or attribute errors.
- All test suites verifying parameter models and domain entity initialization execute successfully without regression.

## Enforcement

- Verified by: Continuous integration type-checking and automated test verification workflows.
- Verified by: Peer code review evaluating domain model structure and typing contracts.
- Violation handling: Pull requests introducing raw parameter dictionaries or untyped data classes will be blocked during review.
- Violation handling: Continuous integration failures on type verification must be resolved prior to merging.
- Exception process: Submit an architectural proposal detailing why dataclass modeling is unsuited for the specific component, subject to maintainer consensus.
