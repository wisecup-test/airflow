---
version: 2
name: baff74b1-98fb-4b80-99f5-3452abdea313-yaml-library-adoption-for-structured-metadata-and-configuration-ingestion-development-utilities-documentation-extensions-test-harnesses
description: "yaml Library Adoption for Structured Metadata and Configuration Ingestion: Development Utilities Documentation Extensions Test Harnesses"
metadata:
  actual:
    adr_id: baff74b1-98fb-4b80-99f5-3452abdea313
    status: accepted
    candidate_group_id: a52f5af7-a290-4157-a662-cea7093b1cdb
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/**
---

# yaml Library Adoption for Structured Metadata and Configuration Ingestion: Development Utilities Documentation Extensions Test Harnesses

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/**`
- `src/tests_common/**`

## Context

- Declarative configuration and metadata files are used across repository documentation build utilities, packaging helpers, and automated test harnesses.
- Parsing structured declarative content requires a standardized deserialization library to ensure uniform access patterns and avoid idiosyncratic format parsers.
- Unrestricted deserialization in dynamic environments introduces security risks, necessitating a standardized safe ingestion practice across internal tooling.

## Problem Statement

Internal developer utilities, documentation generators, and test harnesses require a standardized, robust mechanism to read declarative metadata and configuration documents. Without a uniform library adoption and consistent safe deserialization pattern, disparate components risk adopting heterogeneous parsers or unsafe loading methods that expose the repository tooling to arbitrary code execution vulnerabilities and inconsistent data representations.

## Decision

- **R-LM-001** MUST: All development utilities, documentation extensions, and test harnesses that ingest structured configuration documents MUST use the yaml module and its safe loading functions for data deserialization.

## Policy Block

In scope:

- Developer tooling, documentation build extensions, and test utilities that ingest structured configuration and metadata files.
- Infrastructure helper modules responsible for parsing declarative package metadata and environment configurations.

Out of scope:

- Components utilizing non-declarative serialization formats or binary data protocols.
- Native language modules that do not process external structured metadata files.

Exceptions:

- **EXC-20-001** A specialized configuration loader requires custom type constructors for domain-specific schema tags and cannot use default safe loading.
  Approval: Repository maintainers and security review team.
  Documentation: Architectural rationale recorded in the component header explaining custom constructor safety guarantees.

## Rationale

- Adopting the yaml library provides a mature, standard parser for human-readable declarative configuration across all internal utilities.
- Restricting deserialization to yaml.safe_load eliminates code injection vectors that stem from arbitrary object constructor execution in untrusted documents.
- Centralizing on a consistent parsing protocol simplifies maintenance and aligns documentation generators and test harnesses around shared structural assumptions.

## Consequences

Positive:

- Ensures safe deserialization across all developer tooling and test infrastructure without risking arbitrary code execution.
- Maintains consistent structured data extraction contracts across documentation generation and testing frameworks.
- Reduces parsing defects by standardizing on a single established structured configuration library.

Negative:

- Requires developers to adhere strictly to safe loading interfaces, preventing ad-hoc object serialization convenience.
- Adds dependency management overhead requiring strict lock artifact inspection across developer environments.

## Alternatives

- Standard library JSON deserializer for all configuration documents (rejected)
  Rejected because: JSON lacks support for comments, multi-line strings, and human-friendly syntax needed for complex configuration and metadata files.
  When valid: When metadata is exclusively consumed and produced by automated systems without manual authoring.
- Unrestricted YAML loading allowing native object reconstruction (rejected)
  Rejected because: Allows arbitrary code execution and object instantiation during file parsing, posing unacceptable security risks.
  When valid: When internal serialization requires stateful class instance reconstruction within an isolated, trusted process.

## Risks

- Developers might inadvertently invoke unsafe deserialization methods when handling complex nested configurations.
  Mitigation: Enforce static analysis rules and automated linting that reject calls to unsafe loading interfaces.
  Owner: Core maintainers
- Incompatibilities between library releases could alter parser behavior or tag resolution.
  Mitigation: Strictly adhere to the lock artifact verification protocol before upgrading dependencies.
  Owner: Infrastructure and tooling team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- When reading declarative files, open the input stream in text mode and pass the stream or content directly to yaml.safe_load rather than reading into unvalidated intermediaries.
- Combine deserialization calls with dictionary lookup safeguards or formal schema validation to gracefully catch missing mandatory configuration keys.

## Continuation Context

Verify commands:

- Discover the project static analysis script in the repository configuration and execute it to verify that no unsafe deserialization calls are present in the codebase.
- Discover and execute the repository test runner script to validate that configuration parsing utilities correctly deserialize structured metadata files.
- Locate the repository dependency validation script and execute it to confirm that all installed module versions match the resolved lock artifact.

Accept when:

- Static analysis passes across all internal utility and test modules with zero detected references to unsafe YAML deserialization interfaces.
- All automated tests covering configuration ingestion, documentation extensions, and test utilities pass successfully.
- All library imports match the declared and resolved dependency specifications in the repository lock artifact.

## Enforcement

- Verified by: Automated static analysis checks in the continuous integration pipeline scanning for prohibited loader functions.
- Verified by: Peer code review for all modifications touching developer tooling, documentation extensions, or test infrastructure.
- Violation handling: Pull requests containing unsafe deserialization patterns or unverified library usages are blocked from merging until corrected.
- Violation handling: Security alerts are automatically triggered if unsafe loading patterns are introduced.
- Exception process: Exceptions require documented justification submitted via an architectural review issue and explicit approval from repository maintainers.
