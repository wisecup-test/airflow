---
version: 2
name: cross-cutting-model-implementations-cache-deserialized-bb0e
description: "SerializedDagModel Payload Hashing and Integrity Verification: Model Implementations Cache Deserialized Dictionary Representations"
metadata:
  actual:
    adr_id: bb0edeb4-3e6a-4850-83b9-c35899cfb0d8
    status: accepted
    candidate_group_id: 1e964b15-9872-41c3-836f-d17c05317f56
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# SerializedDagModel Payload Hashing and Integrity Verification: Model Implementations Cache Deserialized Dictionary Representations

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Serialization and persistence of workflow models containing structured task and dependency definitions.
- Version comparison and state synchronization between processing workers and persistent database storage.

### Rules

- **R-A-001** MAY: Model implementations MAY cache deserialized dictionary representations in memory to bypass decompression overhead when payload properties are accessed repeatedly during the same execution cycle.

### Verify

- Discover and run the repository test target verifying model hashing, serialization normalization, and change detection logic.
- Discover and execute the project database integration suite to validate write avoidance when persisting unchanged serialized payloads.

**Accept when:**

- Model hashing consistently yields identical digests for identical payloads across variable dictionary orderings.
- Persistence write routines detect unchanged digests and skip database update operations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration suites testing model serialization and database synchronization routines.
- Verified by: Peer code review verifying compliance with canonical sorting and payload hashing constraints.
- Violation handling: Pull requests omitting deterministic hashing or bypassing pre-write change detection checks are rejected by continuous integration.
- Violation handling: Direct model updates bypassing digest generation are flagged as architecture violations during code inspection.
- Exception process: Exceptions for non-hashed ephemeral models require formal review and approval by the architecture and persistence leads with documented rationale.
</enforcement>
