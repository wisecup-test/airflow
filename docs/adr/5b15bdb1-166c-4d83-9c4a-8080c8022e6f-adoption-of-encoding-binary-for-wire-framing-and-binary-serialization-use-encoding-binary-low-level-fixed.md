---
version: 2
name: 5b15bdb1-166c-4d83-9c4a-8080c8022e6f-adoption-of-encoding-binary-for-wire-framing-and-binary-serialization-use-encoding-binary-low-level-fixed
description: "Adoption of encoding/binary for Wire Framing and Binary Serialization: Use Encoding Binary Low Level Fixed"
metadata:
  actual:
    adr_id: 5b15bdb1-166c-4d83-9c4a-8080c8022e6f
    status: accepted
    candidate_group_id: 6cadd6dc-a567-4109-be84-7ff55105afce
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - internal/bundlefooter/**
      - pkg/execution/**
---

# Adoption of encoding/binary for Wire Framing and Binary Serialization: Use Encoding Binary Low Level Fixed

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `go-sdk`

Applies to:
- `internal/bundlefooter/**`
- `pkg/execution/**`

## Context

- The software development kit requires deterministic binary framing for protocol envelopes and bundle footers across boundary interfaces.
- Heterogeneous data exchanges require explicit byte order and field alignment to prevent corruption across different processor architectures.
- Core framing components require efficient streaming serialization without external runtime dependencies or unbounded memory allocation.

## Problem Statement

Data exchanges and bundle verification mechanisms require deterministic, low-overhead binary serialization with explicit endianness. Ad-hoc serialization or unstructured bit-manipulation creates maintainability and cross-platform compatibility issues, while high-level reflection-based serializers introduce unacceptable memory allocations and schema overhead for fixed-width framing.

## Decision

- **R-LM-001** MUST: MUST use encoding/binary for low-level fixed-width numeric and binary layout encoding across frame headers, footers, and wire boundaries.

## Policy Block

In scope:

- Fixed-width binary envelope headers, bundle footers, and frame boundaries within software development kit modules.
- Binary deserialization and validation routines handling incoming wire frames.

Out of scope:

- High-level application domain payloads that utilize structured serialization formats.
- Human-readable diagnostic logs and text configuration serialization.

Exceptions:

- **EXC-20-001** A payload requires dynamic arbitrary-depth data structures where fixed-width binary encoding cannot represent the schema efficiently.
  Approval: Architecture Review Board
  Documentation: Document format rationale and schema specifications in the module architecture documentation.

## Rationale

- Using encoding/binary standardizes endianness handling and fixed-layout binary encoding without introducing third-party runtime dependencies for core framing.
- Standard writer and reader interfaces provide clean boundary decoupling and allow streaming over streams, buffers, and cryptographic hash writers.
- Strict fixed-width binary validation prevents malformed frames from causing unexpected runtime allocations or panics during execution processing.

## Consequences

Positive:

- Provides deterministic wire representation across differing machine architectures through explicit byte order specification.
- Minimizes memory allocations by streaming directly into destination writer interfaces.
- Isolates binary wire format encoding contracts into dedicated modular abstractions.

Negative:

- Requires manual field mapping and offset accounting for complex composite structures compared to schema-driven code generation.
- Increases developer responsibility for managing buffer boundaries and length validation.

## Alternatives

- Manual bit-shifting and direct slice byte indexing (rejected)
  Rejected because: Manual bit-shifting is error-prone, difficult to audit, and lacks standard endianness abstractions.
  When valid: Micro-optimizations where absolute minimum CPU cycles are required on hot paths after profiling.
- Reflection-based object serialization (rejected)
  Rejected because: Reflection incurs significant runtime allocation overhead and produces non-deterministic field ordering across compiler updates.
  When valid: High-level domain objects with complex polymorphic structures where wire framing efficiency is not critical.

## Risks

- Silent data corruption resulting from incorrect byte order specification between producers and consumers
  Mitigation: Enforce explicit byte order constants in serialization constructors and require comprehensive round-trip tests
  Owner: Core Engineering Team
- Buffer overflow or out-of-bounds panics during untrusted frame deserialization
  Mitigation: Enforce boundary checks and size validation before reading payload bytes from input streams
  Owner: Security and Platform Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Structure binary framing components around reader and writer interfaces to allow seamless chaining with cryptographic hashing and verification.
- Define explicit constants for fixed header sizes, magic numbers, and footer trailers to prevent magic literals across serialization routines.

## Continuation Context

Verify commands:

- Discover and execute the project repository test runner to validate serialization and deserialization round-trip suites.
- Discover and execute the project repository static analysis suite to verify binary encoding compliance and endianness specifications.

Accept when:

- All binary framing and serialization test suites pass with zero validation failures.
- Static analysis checks pass with no non-deterministic binary manipulation warnings.

## Enforcement

- Verified by: Automated pull request checks running the project test suite and static analysis linters.
- Verified by: Peer review by core platform engineers verifying binary protocol adherence.
- Violation handling: Pull requests introducing ad-hoc binary parsing or violating explicit endianness rules will be blocked until refactored.
- Exception process: Submit an architectural exception request detailing the structural serialization requirement and obtain approval from the Architecture Review Board.
