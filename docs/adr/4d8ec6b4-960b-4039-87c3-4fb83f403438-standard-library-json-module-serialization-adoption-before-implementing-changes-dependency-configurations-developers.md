---
version: 2
name: 4d8ec6b4-960b-4039-87c3-4fb83f403438-standard-library-json-module-serialization-adoption-before-implementing-changes-dependency-configurations-developers
description: "Standard Library JSON Module Serialization Adoption: Before Implementing Changes Dependency Configurations Developers"
metadata:
  actual:
    adr_id: 4d8ec6b4-960b-4039-87c3-4fb83f403438
    status: accepted
    candidate_group_id: 8eb0174e-c423-4bbb-ad76-ef4420377eca
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Standard Library JSON Module Serialization Adoption: Before Implementing Changes Dependency Configurations Developers

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- The platform coordinates distributed workflow execution across distinct process boundaries including API servers, CLI execution commands, and task workers.
- Structured runtime data, including configuration overrides, task parameters, asset keys, and audit logs, must be serialized and deserialized across these distributed boundaries.
- The codebase exhibits pervasive reliance on the standard json module across forty-eight core components, establishing a uniform baseline serialization protocol across all platform subpackages.

## Problem Statement

Distributed workflow execution and HTTP API interfaces require a standardized, secure, and ubiquitous data serialization format to exchange state, parameters, and configuration without introducing fragile binary dependencies, arbitrary code execution vulnerabilities, or version incompatibilities across heterogeneous worker nodes.

## Decision

- **R-LM-001** MUST: Before implementing changes to dependency configurations, developers MUST discover the project dependency lock artifact and verify the exact resolved environment specification.

## Policy Block

In scope:

- Serialization and deserialization of runtime configuration objects, credentials, and authentication state.
- Inter-process payload exchange across execution API endpoints, CLI command interfaces, and task instances.
- Storage and retrieval of serialized asset profile identifiers and workflow parameters.

Out of scope:

- Binary stream transfers and compressed data partitions that utilize dedicated archive formats.
- Relational database column persistence handled directly through database schema abstraction layers.

Exceptions:

- **EXC-20-001** A performance-critical data path demonstrates documented parsing bottlenecks and requires a specialized high-throughput serializer.
  Approval: Architecture Review Board
  Documentation: Benchmark analysis and architectural assessment documented in an architecture decision record.

## Rationale

- Adopting the standard json module ensures zero external dependencies and guarantees uniform availability across any standard runtime environment.
- Text-based JSON structures mitigate arbitrary code execution risks that accompany binary object serialization protocols when processing untrusted inputs.
- A single platform-wide serialization baseline simplifies contract debugging, schema migration, and cross-language interoperability across user interfaces and service clients.

## Consequences

Positive:

- Eliminates external binary parsing dependencies across worker nodes and edge environments.
- Guarantees human-readable serialization formats for operational debugging, logging, and database inspection.
- Protects distributed execution components from object deserialization security vulnerabilities.

Negative:

- Introduces higher CPU parsing overhead and serialization latency on exceptionally large payload structures compared to compiled serializers.
- Lacks native support for complex binary types, requiring explicit serialization wrappers for custom date and object structures.

## Alternatives

- Adopt binary object serialization protocols for internal state transfer (rejected)
  Rejected because: Exposes systems to arbitrary code execution vulnerabilities and prevents human inspection of serialized execution payloads.
  When valid: Valid only in closed environments requiring extreme throughput with non-textual data structures.
- Adopt third-party compiled JSON parsing libraries across all subpackages (rejected)
  Rejected because: Adds native compilation requirements and packaging complexity across diverse deployment targets without demonstrated necessity for standard payload sizes.
  When valid: Valid when profiling data confirms that JSON parsing throughput constitutes the primary system bottleneck.

## Risks

- Unvalidated JSON payloads could cause unhandled exceptions during execution parsing.
  Mitigation: Enforce explicit exception handling and validation decorators around all json.loads and json.load call sites.
  Owner: Core Platform Engineering
- Serialization of custom non-primitive types fails without explicit type conversion handlers.
  Mitigation: Require structured models to convert custom types into primitive dictionaries prior to invoking json.dumps.
  Owner: Core Platform Engineering

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Always validate that inputs to json.loads are strings or byte sequences and handle decode errors explicitly.
- When writing serialized JSON configurations to disk in concurrent environments, employ advisory file locking to prevent partial write corruption.
- Mask sensitive keys prior to serializing dictionary structures into log records or telemetry payloads.

## Continuation Context

Verify commands:

- Discover and run the project static analysis suite to check for compliant json module usage and error handling.
- Discover and execute the project test runner across serialization and API test suites to verify payload encoding and decoding behavior.

Accept when:

- All serialization and deserialization routines execute without unhandled decoding errors across test suites.
- Static analysis validates that no unsafe deserialization functions are introduced at external or inter-process boundaries.

## Enforcement

- Verified by: Automated continuous integration checks executing static analysis and test validation suites.
- Verified by: Mandatory peer code review verifying adherence to serialization rules and error handling conventions.
- Violation handling: Pull requests introducing unhandled deserialization calls or unapproved serializers are blocked until corrected.
- Exception process: Exceptions must be formally submitted to the architecture review board with performance benchmarks demonstrating necessity.
