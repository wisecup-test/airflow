---
version: 2
name: 95e7bf59-9823-4ce0-90e4-7811285ad07a-adoption-of-dataclasses-module-for-structured-parameter-and-model-definitions-data-containers-declared-dataclasses-not-define
description: "Adoption of dataclasses Module for Structured Parameter and Model Definitions: Data Containers Declared Dataclasses Not Define"
metadata:
  actual:
    adr_id: 95e7bf59-9823-4ce0-90e4-7811285ad07a
    status: accepted
    candidate_group_id: 1bf56d00-aa7e-4970-863a-6e9e23fcb661
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
      - registry/**
---

# Adoption of dataclasses Module for Structured Parameter and Model Definitions: Data Containers Declared Dataclasses Not Define

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/**`
- `registry/**`

## Context

- The system manages diverse operational, build, and environment parameters across internal tooling and contract boundaries.
- Ad-hoc dictionaries and manually written classes lead to repetitive initialization boilerplate, inconsistent type validation, and higher maintenance overhead.
- The standard library provides the dataclasses module, which supplies declarative class decoration, automated method generation, and structured attribute management.
- Parameter schemas require hierarchical composition, field defaults, and structured transformation logic to generate downstream execution configurations.

## Problem Statement

Managing complex operational and build configurations requires a unified, maintainable approach to structured data modeling. Without a standardized data container pattern, parameter definitions suffer from manual initializer boilerplate, lack of consistent type annotations, and error-prone inheritance across build variants.

## Decision

- **R-LM-001** MUST_NOT: Data containers declared as dataclasses MUST NOT define custom initialization methods that duplicate or override the generated initializer signature without explicit architectural rationale.

## Policy Block

In scope:

- All internal parameter models, build configuration structures, and execution option containers.

Out of scope:

- Data transfer schemas that strictly require third-party schema validation libraries for incoming untrusted network payloads.

Exceptions:

- **EXC-20-001** When third-party schema validation engines are required for parsing and sanitizing untrusted external payloads.
  Approval: Architecture Review Board
  Documentation: Record the external validation requirements and mapping layer in the module documentation.

## Rationale

- The dataclasses module standardizes declarative parameter definitions across the codebase, reducing boilerplate code and making field contracts explicit.
- Native support for inheritance enables clean separation between base build parameters and specialized target parameters.
- Built-in lifecycle hooks like __post_init__ provide clean extension points for attribute normalization without requiring manual initializer maintenance.
- Using standard library data modeling minimizes external third-party dependency surface for developer tooling and configuration modules.

## Consequences

Positive:

- Eliminates repetitive initializer boilerplate across parameter classes.
- Ensures type annotations are systematically declared and accessible for static analysis.
- Enables clean inheritance hierarchies for specialized execution parameters.
- Minimizes external dependency coupling for internal developer tooling.

Negative:

- Standard dataclasses do not perform automatic runtime type coercion or validation on attribute assignment.
- Inheritance with default values can impose ordering constraints on field declarations in subclasses.

## Alternatives

- Manual class definitions with custom initializers and representation methods (rejected)
  Rejected because: Introduces substantial boilerplate, increases maintenance overhead, and invites inconsistencies in parameter comparison and representation.
  When valid: When targeting legacy execution environments where standard library class decorators are unavailable.
- Untyped dictionary structures for parameter passing (rejected)
  Rejected because: Lacks static type safety, obfuscates available parameters, and permits silent attribute typos during runtime configuration.
  When valid: When handling arbitrary, unstructured key-value payloads with dynamic keys.
- External data validation frameworks for all internal parameter models (rejected)
  Rejected because: Adds unnecessary external dependency overhead and execution cost for internal build parameter objects where runtime schema validation is redundant.
  When valid: When ingesting untrusted external input from remote network boundaries.

## Risks

- Default field ordering in subclass hierarchies causing inheritance declaration errors.
  Mitigation: Structure base classes with keyword-only fields or ensure defaulted attributes are consistently placed after non-defaulted fields across subclasses.
  Owner: engineering team
- Accidental mutation of shared default objects across multiple instances.
  Mitigation: Enforce the use of the field function with a default factory for all mutable collection defaults via static analysis.
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
- When declaring fields with mutable default values such as lists, mappings, or sets, always configure the field using the field function with a default factory callable to avoid instance state contamination.
- Use computed properties within dataclass containers to encapsulate conversion logic from raw parameters into argument arrays, keeping invocation assembly decoupled from raw state storage.

## Continuation Context

Verify commands:

- Discover the repository static type analysis script from the project configuration and execute it across all parameter modules to verify field type compliance.
- Discover the test execution entrypoint defined in the project configuration and run the unit test suite covering parameter instantiation and inheritance.

Accept when:

- Static type checking passes across all parameter class declarations without typing errors.
- All unit tests validating parameter instantiation, default values, and computed properties execute successfully.

## Enforcement

- Verified by: Automated static type checking integrated into continuous integration pipelines.
- Verified by: Peer code review verifying adherence to dataclass conventions and absence of manual initializer boilerplate.
- Violation handling: Pull requests introducing manual initializer boilerplate or untyped parameter dictionaries will be blocked by static checks or review feedback.
- Violation handling: Non-compliant classes must be refactored to use standard dataclass definitions before merging.
- Exception process: Submit an exception request detailing why standard dataclasses cannot meet the structural or performance requirements.
- Exception process: Obtain written approval from the architecture review group and document the exception in the module header.
