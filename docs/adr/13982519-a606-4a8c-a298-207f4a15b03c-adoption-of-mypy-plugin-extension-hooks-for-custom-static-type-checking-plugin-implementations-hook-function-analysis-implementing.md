---
version: 2
name: 13982519-a606-4a8c-a298-207f4a15b03c-adoption-of-mypy-plugin-extension-hooks-for-custom-static-type-checking-plugin-implementations-hook-function-analysis-implementing
description: "Adoption of mypy.plugin Extension Hooks for Custom Static Type Checking: Plugin Implementations Hook Function Analysis Implementing"
metadata:
  actual:
    adr_id: 13982519-a606-4a8c-a298-207f4a15b03c
    status: accepted
    candidate_group_id: 31113f0d-d227-40bd-8bde-4ea961fa1bb3
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - mypy/src/airflow_mypy/plugins/**
---

# Adoption of mypy.plugin Extension Hooks for Custom Static Type Checking: Plugin Implementations Hook Function Analysis Implementing

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `mypy/src/airflow_mypy/plugins/**`

## Context

- Framework runtime behavior introduces dynamic idioms such as task decorators and deferred value passing that cannot be fully expressed through standard static type annotations alone.
- Direct type annotations on dynamic callable signatures without custom type evaluation either produce false positive type errors or require degrading signatures to unconstrained types.
- The codebase establishes dedicated type checking plugins inheriting from mypy.plugin.Plugin to customize AST type inference without altering runtime application logic.

## Problem Statement

Standard static type checking in Python lacks native mechanisms to transparently resolve dynamic decorator argument transformations and deferred task dependency outputs at compile time. Without dedicated type checker extensions, developers must either compromise static type safety by using unconstrained dynamic types or endure false-positive static verification errors across orchestration workflows.

## Decision

- **R-LM-001** MUST: Plugin implementations MUST hook into function analysis by implementing get_function_hook to intercept callable invocations requiring custom signature transformations.

## Policy Block

In scope:

- Type checking plugin modules extending static analysis for framework-specific DSLs, decorators, and deferred execution arguments.

Out of scope:

- Standard application and runtime business logic that can be expressed using native type hints and standard library typing constructs.

Exceptions:

- **EXC-20-001** A framework construct can be modeled accurately using standard typing constructs without custom type checker hook intervention.
  Approval: Architecture Review Board
  Documentation: Document rationale in the module documentation where standard typing is employed.

## Rationale

- Implementing custom hooks via mypy.plugin provides fine-grained control over function, method, and attribute resolution during static analysis passes.
- Bridging deferred runtime values to expected target types within static analysis prevents widespread type errors while maintaining strict verification standards across user codebases.
- Encapsulating type analysis logic within separate plugin classes preserves a clean boundary between runtime workflow execution and build-time static verification.

## Consequences

Positive:

- Static verification remains accurate for high-level workflow abstractions and dynamic decorator signatures.
- User-facing task definitions and downstream argument passing retain strict type safety without unnecessary boilerplate casts.
- Decouples static analysis customization from production runtime dependencies.

Negative:

- Custom plugin implementations are tightly coupled to the internal APIs and AST representations of mypy.plugin and mypy.types.
- Upgrading the static type checker requires maintaining and verifying plugin hook compatibility against upstream API changes.
- Increases development complexity by requiring maintainers to understand internal compiler and type synthesis semantics.

## Alternatives

- Rely solely on standard typing annotations and type unions in application signatures (rejected)
  Rejected because: Standard annotations fail to model deferred argument conversion accurately without requiring invasive runtime type conversions or unconstrained types across public APIs
  When valid: When all framework constructs and dataflows conform strictly to standard static type definitions without dynamic runtime resolution
- Disable type checking on dynamic workflow definitions using blanket static ignore directives (rejected)
  Rejected because: Blanket suppression removes type safety guarantees and allows undetectable type regressions to enter workflows
  When valid: During rapid prototyping or temporary debugging phases before production release

## Risks

- Changes to upstream type checker hook signatures and type representations could break static analysis during dependency upgrades.
  Mitigation: Pin type checking dependencies to authoritative lock artifact versions and run dedicated regression tests on plugin hooks prior to upgrading.
  Owner: Platform Engineering
- Improper type synthesis in plugin callbacks could mask genuine type errors by treating complex structures as unconstrained types.
  Mitigation: Restrict fallback unconstrained type assignments to strictly necessary deferred attributes and construct precise compound types whenever feasible.
  Owner: Architecture Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Subclass the Plugin base class and register callback hooks specifically targeted to known fully qualified function and attribute names.
- Use type synthesis methods from mypy.types to evaluate dynamic signatures and construct valid compound types accurately.

## Continuation Context

Verify commands:

- Discover and run the project static type analysis verification suite using the repository test runner script.
- Discover and execute plugin unit test fixtures against target decorated signatures and attribute lookups.

Accept when:

- All plugin hook callbacks return valid type representations without raising unhandled exceptions during static analysis passes.
- Static type analysis passes cleanly across modules employing framework decorators and deferred argument passing without requiring manual suppression directives.

## Enforcement

- Verified by: Continuous integration static analysis pipelines executing automated type verification.
- Verified by: Automated test suites covering plugin hook resolution logic.
- Violation handling: Pull requests introducing unhandled static typing failures or unapproved suppression directives are blocked from merging.
- Violation handling: Non-compliant type definitions must be adjusted to align with plugin hook conventions.
- Exception process: Exceptions must be documented with an architectural justification and approved by the core engineering maintainers through code review.
