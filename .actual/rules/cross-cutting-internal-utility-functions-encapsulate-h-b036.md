---
version: 2
name: cross-cutting-internal-utility-functions-encapsulate-h-b036
description: "Standardize SHA-256 Digest and Key Derivation on hashlib: Internal Utility Functions Encapsulate Hashlib Sha256"
metadata:
  actual:
    adr_id: b0360f94-aa6f-4d92-a0e0-d9728a4c3355
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

# Standardize SHA-256 Digest and Key Derivation on hashlib: Internal Utility Functions Encapsulate Hashlib Sha256

Scope: applies to `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/test_utils/**`.

These rules are ALWAYS ACTIVE for:
- Shared utility modules requiring deterministic key derivation from input strings.
- Build and documentation extensions managing cache keys for remote assets and build artifacts.

### Rules

- **R-LM-001** MAY: Internal utility functions MAY encapsulate hashlib.sha256 invocation pipelines into reusable helper abstractions within their local module boundaries.

### Verify

- Discover and execute the repository static analysis and test validation suites to confirm hashlib.sha256 usage compliance.
- Inspect repository module dependencies using the project build inspection facilities to ensure no redundant hashing packages are introduced.

**Accept when:**

- All deterministic content hashing and key derivation implementations invoke hashlib.sha256 with explicit byte encoding.
- Repository test suites and static analysis checks pass without errors or unhandled encoding warnings.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks in the continuous integration pipeline.
- Verified by: Peer code reviews on all pull requests introducing hashing or key generation.
- Violation handling: Pull requests containing unapproved hash algorithms or non-encoded hashlib invocations are blocked until remediated.
- Exception process: Formal review and approval by architectural deciders documenting why a specialized hashing algorithm is required.
</enforcement>
