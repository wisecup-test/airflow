---
version: 2
name: 7d7365b3-6157-460d-ae3e-047d93eab91a-standardize-sha-256-digest-and-key-derivation-on-hashlib-codebase-standardize-hashlib-sha256-deterministic-content
description: "Standardize SHA-256 Digest and Key Derivation on hashlib: Codebase Standardize Hashlib Sha256 Deterministic Content"
metadata:
  actual:
    adr_id: 7d7365b3-6157-460d-ae3e-047d93eab91a
    status: accepted
    candidate_group_id: e40eed8f-f9d5-4053-a67f-12fcb25505f4
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# Standardize SHA-256 Digest and Key Derivation on hashlib: Codebase Standardize Hashlib Sha256 Deterministic Content

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/**`
- `src/tests_common/test_utils/**`

## Context

- Shared testing utilities and documentation build extensions require deterministic identifier generation for caching remote assets and deriving encryption keys.
- Disparate hashing approaches create risks of collision, inconsistent cache invalidation, and incompatible key derivation across subsystems.
- Standardizing on hashlib.sha256 establishes consistent digest behavior across platforms and runtime configurations without adding external dependencies.

## Problem Statement

Without a standardized hashing and digest derivation mechanism, disparate modules risk adopting fragmented, platform-dependent, or non-deterministic approaches for cache keys and test key derivation. An architectural standard is required to ensure collision resistance, deterministic outputs across environments, and dependency-free operation in shared utilities.

## Decision

- **R-LM-001** MUST: The codebase MUST standardize on hashlib.sha256 for all deterministic content hashing, cache key calculation, and derived key material across shared utilities and extension modules.

## Policy Block

In scope:

- Shared utility modules requiring deterministic key derivation from input strings.
- Build and documentation extensions managing cache keys for remote assets and build artifacts.

Out of scope:

- Security-sensitive password hashing and long-term secret storage requiring salted, slow key derivation functions.
- Performance-critical streaming workloads where non-cryptographic checksum algorithms are explicitly specified by external format protocols.

## Rationale

- Adopting hashlib.sha256 provides a stable, built-in, collision-resistant cryptographic hash implementation that requires no third-party package dependencies.
- Ensuring byte-level string encoding prior to digest computation prevents encoding-dependent hash divergences across operating systems and locales.
- Standardizing the digest algorithm ensures that cache identifiers and derived test keys behave deterministically across isolated test and build runs.

## Consequences

Positive:

- Consistent and deterministic cache key and derived key calculation across shared modules.
- Zero external dependency footprint for hashing and digest generation.
- Reduced risk of hash collisions compared to non-cryptographic hash functions.

Negative:

- Higher computational overhead relative to non-cryptographic hashing algorithms for large data sets.
- Requirement for explicit string-to-byte encoding handling on every hashing call.

## Alternatives

- Adopting third-party hashing libraries (rejected)
  Rejected because: Introduces unnecessary external dependency overhead and maintenance burdens when core library capabilities already fulfill all cryptographic digest requirements
  When valid: When specialized cryptographic algorithms or high-throughput specialized SIMD hashing functions not present in core modules are required
- Using non-cryptographic hashing functions (rejected)
  Rejected because: Higher risk of hash collisions and lack of cryptographic security guarantees necessary for deterministic key derivation
  When valid: When hashing in-memory hash table keys where non-cryptographic performance is the sole consideration

## Risks

- Passing strings without explicit character encoding can lead to platform-specific encoding errors or divergent digests.
  Mitigation: Enforce explicit byte encoding prior to passing inputs into hashlib digest functions via linting and code review.
  Owner: engineering team
- Applying SHA-256 inappropriately for password storage or sensitive secrets where salted, slow hashing is mandatory.
  Mitigation: Define clear policy scoping that restricts this pattern to content caching and utility key generation, disallowing password hashing.
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
- Always pass explicit UTF-8 byte encodings to hashlib.sha256 to ensure cross-platform consistency of digests.
- When producing string representations of raw digests for filesystem paths or URLs, use standard hex digests or URL-safe base64 encodings.

## Continuation Context

Verify commands:

- Discover and execute the repository static analysis and test validation suites to confirm hashlib.sha256 usage compliance.
- Inspect repository module dependencies using the project build inspection facilities to ensure no redundant hashing packages are introduced.

Accept when:

- All deterministic content hashing and key derivation implementations invoke hashlib.sha256 with explicit byte encoding.
- Repository test suites and static analysis checks pass without errors or unhandled encoding warnings.

## Enforcement

- Verified by: Automated static analysis checks in the continuous integration pipeline.
- Verified by: Peer code reviews on all pull requests introducing hashing or key generation.
- Violation handling: Pull requests containing unapproved hash algorithms or non-encoded hashlib invocations are blocked until remediated.
- Exception process: Formal review and approval by architectural deciders documenting why a specialized hashing algorithm is required.
