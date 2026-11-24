---
version: 2
name: ab7b8702-991b-4bae-b63b-33d2e0b73112-dataclasses-configuration-domain-modeling-before-introducing-external-dependencies-versioned-helper
description: "dataclasses Configuration Domain Modeling: Before Introducing External Dependencies Versioned Helper"
metadata:
  actual:
    adr_id: ab7b8702-991b-4bae-b63b-33d2e0b73112
    status: accepted
    candidate_group_id: 9760a0b9-2198-4e1b-947b-06631217706c
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/ctl/commands/**
---

# dataclasses Configuration Domain Modeling: Before Introducing External Dependencies Versioned Helper

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-ctl`

Applies to:
- `src/airflowctl/ctl/commands/**`

## Context

- Configuration management commands require structured representations of configuration parameters and parameter changes.
- Entity representations must be shared between command handlers and validation inspection routines.
- The evidence indicates typed container constructs used in a single command module without broader cross-package adoption.

## Problem Statement

Command handlers handling configuration parameters require structured, type-safe data representations to prevent schema errors and decouple domain data models from terminal display logic.

## Decision

- **R-DM-001** MUST: Before introducing external dependencies or versioned helper libraries for domain modeling, consumers MUST inspect the repository lock artifact to determine the exact resolved dependency version.

## Policy Block

In scope:

- Domain entity representations within configuration inspection and command handlers
- Structured value objects passed between client interfaces and validation routines

Out of scope:

- Unstructured dynamic payloads exchanged across external network boundaries
- Dynamic runtime state tracking objects requiring mutable behavior

## Rationale

- Using typed containers for domain entities ensures compile-time and static inspection of parameter attributes.
- Standard data modeling constructs minimize boilerplate while providing clean contract boundaries between components.
- The detected evidence shows localized use of typed structures for domain models within a single command implementation.

## Consequences

Positive:

- Enforces explicit structural schemas and property typing for domain entities.
- Reduces boilerplate code for object initialization, attribute access, and equality comparisons.

Negative:

- Single-file adoption without repository-wide conventions risks inconsistent modeling choices across modules.
- Refactoring legacy unstructured parameters to typed structures requires initial updating of calling code.

## Alternatives

- Untyped dictionary structures for configuration modeling (rejected)
  Rejected because: Lacks static type safety, making attribute access errors and schema drift undetectable prior to runtime execution.
  When valid: When handling dynamic unstructured payloads where schema properties cannot be known prior to runtime.
- Custom domain classes with explicit manual constructor boilerplate (rejected)
  Rejected because: Introduces redundant structural initialization and equality comparison code compared to standard container decorators.
  When valid: When custom encapsulation invariants require complex state transitions during instantiation.

## Risks

- Inconsistent domain modeling styles emerging across separate command modules due to single-file pattern adoption.
  Mitigation: Establish repository-wide static analysis checks to enforce uniform data class modeling across domain definitions.
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
- Define domain models with explicit attribute type annotations to enable automated static type checking.
- Separate domain value objects from terminal presentation formatting logic.

## Continuation Context

Verify commands:

- Discover and execute the project static analysis test suite to verify domain model type conformity.
- Discover and run the project automated test suite to validate configuration parameter parsing and contract integrity.

Accept when:

- All domain entity definitions adhere to typed container declarations without type errors.
- Automated unit tests covering configuration validation and parameter models pass without failures.

## Enforcement

- Verified by: Static type analysis and linters executed during continuous integration
- Verified by: Peer code review for pull requests modifying domain entities
- Violation handling: Pull requests introducing untyped or inconsistent entity definitions will be blocked in continuous integration until updated to use typed structures.
- Exception process: Submit an architectural review request detailing why structured entity containers cannot satisfy specific runtime constraints.
