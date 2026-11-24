---
version: 2
name: cross-cutting-discover-ecosystem-lock-artifact-project-1f4b
description: "Adoption of encoding/binary for Wire Framing and Binary Serialization: Discover Ecosystem Lock Artifact Project Repository"
metadata:
  actual:
    adr_id: 1f4b5886-630d-4034-b6fc-8563d81c577b
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

# Adoption of encoding/binary for Wire Framing and Binary Serialization: Discover Ecosystem Lock Artifact Project Repository

Scope: applies to `go-sdk/internal/bundlefooter/**`, `go-sdk/pkg/execution/**`.

These rules are ALWAYS ACTIVE for:
- Fixed-width binary envelope headers, bundle footers, and frame boundaries within software development kit modules.
- Binary deserialization and validation routines handling incoming wire frames.

### Rules

- **R-LM-001** MUST: MUST discover the ecosystem lock artifact in the project repository and verify all resolved dependency versions before implementing or modifying serialization modules.

### Verify

- Discover and execute the project repository test runner to validate serialization and deserialization round-trip suites.
- Discover and execute the project repository static analysis suite to verify binary encoding compliance and endianness specifications.

**Accept when:**

- All binary framing and serialization test suites pass with zero validation failures.
- Static analysis checks pass with no non-deterministic binary manipulation warnings.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated pull request checks running the project test suite and static analysis linters.
- Verified by: Peer review by core platform engineers verifying binary protocol adherence.
- Violation handling: Pull requests introducing ad-hoc binary parsing or violating explicit endianness rules will be blocked until refactored.
- Exception process: Submit an architectural exception request detailing the structural serialization requirement and obtain approval from the Architecture Review Board.
</enforcement>
