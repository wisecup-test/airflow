---
version: 2
name: b76f2cb0-9740-46dc-b9a2-6322b209a5c0-adoption-of-core-json-module-for-development-and-testing-utility-serialization-development-testing-utilities-not-introduce-unapproved
description: "Adoption of Core json Module for Development and Testing Utility Serialization: Development Testing Utilities Not Introduce Unapproved"
metadata:
  actual:
    adr_id: b76f2cb0-9740-46dc-b9a2-6322b209a5c0
    status: accepted
    candidate_group_id: 9ca523a2-d426-417d-a982-d3f05a89ad79
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/**
---

# Adoption of Core json Module for Development and Testing Utility Serialization: Development Testing Utilities Not Introduce Unapproved

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/**`
- `src/tests_common/**`

## Context

- Shared development tooling, documentation build extensions, and test utilities require serialization and deserialization of structured configuration, credential files, and diagnostic metadata.
- Introducing third-party serialization dependencies into shared test harnesses increases setup complexity and risks cross-platform build failures across developer environments.
- Standardizing on the core serialization module ensures predictable data interchange across database fixtures, mock client response layers, and system connection helpers.

## Problem Statement

Development and testing utilities across the codebase frequently serialize connection parameters, deserialize diagnostic log payloads, parse validation schemas, and mock client responses. Without an explicit standard, test utilities risk fragmenting across ad-hoc string formatting or introducing heavy external third-party serialization libraries that compromise environment portability and test execution speed.

## Decision

- **R-LM-001** MUST_NOT: Development and testing utilities MUST NOT introduce unapproved external serialization libraries that require binary compilation or introduce environment-specific build dependencies.

## Policy Block

In scope:

- Shared testing utilities and common test harness packages.
- Documentation build extensions and schema verification utilities.
- Mock HTTP clients and external connection helper modules.

Out of scope:

- High-performance production execution engines handling high-volume serialized task message streams.
- Specialized database storage layers utilizing native binary column formats.

Exceptions:

- **EXC-20-001** A system test utility must interoperate with an external service protocol requiring binary serialization formats.
  Approval: Project technical architecture maintainers
  Documentation: Explicit inline reference to the external protocol specification alongside the utility implementation

## Rationale

- Fourteen development support and test utility files demonstrate consistent adoption of core json serialization routines for structured payload manipulation.
- The standard library module eliminates external dependency resolution risks and platform-specific compilation requirements for shared test fixtures.
- Providing standardized serialization interfaces across connection helpers and log analysis ensures interoperability across distinct test suites.

## Consequences

Positive:

- Zero external runtime or compile-time dependencies required for test helpers and build extensions across heterogeneous environments.
- Uniform API conventions for payload encoding and decoding across mock client responses, connection configurations, and log fixtures.
- Elimination of binary dependency wheel build failures during development environment setup and test execution.

Negative:

- Serialization performance is constrained by standard interpreter execution speed rather than optimized compiled C extensions.
- Non-primitive data structures require explicit normalization or custom encoders prior to invocation of serialization routines.

## Alternatives

- Adoption of third-party high-performance binary-compiled serialization libraries (rejected)
  Rejected because: Compiles external C or Rust extensions that introduce unnecessary cross-platform compilation overhead and environment incompatibilities for development and testing utilities.
  When valid: High-throughput production data pipelines processing gigabytes of serialization events per second where parser overhead is a measured bottleneck.
- Ad-hoc string formatting and custom text splitting for structured connection extras and log payloads (rejected)
  Rejected because: Prone to escaping bugs, injection vulnerabilities, and brittle parsing failures when payload schemas evolve.
  When valid: Trivial flat key-value pairs with strictly bounded character sets and no nesting requirements.

## Risks

- Attempting to serialize non-standard Python objects directly without pre-serialization mapping raises serialization errors at runtime.
  Mitigation: Ensure custom domain objects are explicitly transformed into primitive dictionary and list representations prior to serialization.
  Owner: engineering team
- Parsing excessively large test fixture files entirely in memory may cause memory spikes in constrained test runners.
  Mitigation: Use stream decoding routines for large schema files and resource fixtures rather than loading full payloads into memory.
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
- When loading structured files or schemas from repository resources, pass open file handles directly to stream decoding routines rather than reading entire contents into intermediate string buffers.
- When serializing connection extra parameters and diagnostic event fields, ensure dictionary keys and values conform to serializable primitive types prior to invoking string encoding routines.

## Continuation Context

Verify commands:

- Discover the project verification script defined in the repository configuration and execute the static analysis suite across shared development and testing modules.
- Discover the test runner invocation from the project manifest and execute the test suite covering utility serialization functions.

Accept when:

- All shared test helpers and documentation utilities serialize and deserialize structured data using the core json module without raising format exceptions.
- Static analysis verification demonstrates that no unapproved third-party serialization libraries are imported within shared development utility packages.
- Automated test suites exercising connection serialization, schema validation, and diagnostic log inspection pass cleanly in all execution environments.

## Enforcement

- Verified by: Automated static analysis checks flagging unauthorized external serialization imports in shared testing packages.
- Verified by: Code review inspection of utility pull requests handling connection credentials, mock payloads, and diagnostic logs.
- Verified by: Continuous test suite execution verifying serialization compatibility across supported operating environments.
- Violation handling: Flag pull requests introducing unapproved serialization libraries or ad-hoc payload string concatenation as non-compliant.
- Violation handling: Block merge until external serialization imports are replaced with approved core library interfaces.
- Exception process: Submit an architectural proposal detailing why core serialization capabilities cannot satisfy the functional requirements of the utility.
- Exception process: Demonstrate that the proposed serialization alternative introduces no conflicting runtime requirements or build barriers for existing environments.
- Exception process: Obtain approval from the technical steering group and document the exception within project configuration manifests.
