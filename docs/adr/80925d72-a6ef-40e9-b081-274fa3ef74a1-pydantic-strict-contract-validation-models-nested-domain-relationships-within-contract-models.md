---
version: 2
name: 80925d72-a6ef-40e9-b081-274fa3ef74a1-pydantic-strict-contract-validation-models-nested-domain-relationships-within-contract-models
description: "Pydantic Strict Contract Validation Models: Nested Domain Relationships Within Contract Models"
metadata:
  actual:
    adr_id: 80925d72-a6ef-40e9-b081-274fa3ef74a1
    status: accepted
    candidate_group_id: c2be38ea-a209-41e6-baed-8a5520252127
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - registry/**
---

# Pydantic Strict Contract Validation Models: Nested Domain Relationships Within Contract Models

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `registry/**`

## Context

- Domain entities and catalog metadata require rigorous validation boundaries to guarantee structural integrity before persistence or distribution.
- Permissive data models allow silent ingestion of undeclared or misspelled attributes, creating latent schema drift across registry contracts.
- The codebase establishes strict contract definitions using Pydantic BaseModel instances configured to forbid unmodeled attributes across domain entity definitions.
- Centralized validation functions operate on these structured contract models to verify relational integrity, catalog collections, and parameter conformity.

## Problem Statement

Unvalidated external or serialized domain data introduces silent schema drift and runtime corruption when unmodeled fields are permitted to pass into domain contracts without rejection. Without strict structural enforcement at the domain contract boundary, malformed metadata, undocumented attributes, and catalog inconsistencies propagate downstream into operational components, leading to unpredictable failures and compromised data contracts.

## Decision

- **R-BRE-001** MUST: Nested domain relationships within contract models MUST reference typed child contract models rather than unconstrained mapping or collection types.

## Policy Block

In scope:

- All domain contract models representing external registry entities, catalog definitions, and distribution metadata.
- Validation routines that parse, verify, and enforce integrity rules on domain contract structures.

Out of scope:

- Internal transient data structures and local utility mappings that do not represent exported or persisted domain contracts.
- High-throughput streaming payloads where schema validation overhead cannot be tolerated.

Exceptions:

- **EXC-56-001** Legacy external feeds require backward compatibility with undeclared optional attributes during migration periods.
  Approval: Domain architecture review board approval.
  Documentation: Document the temporary permissive schema configuration and transition sunset deadline in the contract model docstring.

## Rationale

- Restricting extra fields via ConfigDict with extra set to forbid prevents unexpected payload drift and catches schema typographical errors at ingestion time.
- Explicit type annotations and Field default factories in Pydantic BaseModel eliminate mutable default argument bugs while maintaining self-documenting contract specifications.
- Hierarchical modeling using specialized nested contracts ensures that structural business rules are validated composably throughout complex domain hierarchies.

## Consequences

Positive:

- Eliminates silent data corruption by immediately rejecting payloads with undeclared attributes.
- Provides explicit, self-documenting contract specifications and automated runtime validation for domain metadata.
- Ensures consistency across domain entities through composable child contract structures.

Negative:

- Imposes schema rigidity requiring explicit model updates whenever source metadata schemas evolve.
- Adds runtime validation execution overhead during entity instantiation and serialization.

## Alternatives

- Standard library dataclasses without extra attribute validation (rejected)
  Rejected because: Standard library dataclasses do not natively reject undeclared keyword arguments or enforce deep type validation at runtime without extensive custom boilerplate.
  When valid: Lightweight internal data structures where validation occurs strictly at external system boundaries.
- Permissive Pydantic models allowing extra attributes (rejected)
  Rejected because: Allows undocumented and misspelled fields to pass into domain stores silently, obscuring contract violations and inducing schema drift.
  When valid: Proxy layers acting as pass-through forwarders of arbitrary upstream vendor payloads.

## Risks

- Breaking upstream contract changes will cause immediate rejection of incoming payloads.
  Mitigation: Establish contract regression test suites and documented exception workflows for schema migrations.
  Owner: engineering team
- Runtime validation overhead on large catalog collections.
  Mitigation: Apply batch validation routines and reuse parsed model representations across execution pipelines.
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
- Define all domain contract models in dedicated contract modules, separating schema definitions from external ingestion pipelines.
- Configure collection fields with Field default factories to guarantee fresh collection instances per contract instance.

## Continuation Context

Verify commands:

- Discover the project test runner configuration and execute test suites targeting contract model validation suites.
- Discover the static type analysis configuration and run the project type checking suite across all domain model packages.
- Discover and run the project schema integrity verification scripts against all sample catalog fixtures.

Accept when:

- All contract models reject instantiation when undeclared attributes are supplied.
- Static type analysis passes across all domain contract definitions with zero type errors.
- All catalog validation test suites execute and pass successfully.

## Enforcement

- Verified by: Automated continuous integration pipeline executing unit tests, contract integrity checks, and static type analysis.
- Verified by: Mandatory peer code review focusing on contract schema definitions and ConfigDict configurations.
- Violation handling: Pull requests introducing contract models without extra set to forbid or lacking strict type annotations are blocked from merging.
- Violation handling: Runtime contract validation failures trigger immediate rejection of invalid data payloads with descriptive error traces.
- Exception process: Submit an exception request to the domain architecture review board detailing the backward compatibility need, proposed mitigation, and timeline for schema alignment.
