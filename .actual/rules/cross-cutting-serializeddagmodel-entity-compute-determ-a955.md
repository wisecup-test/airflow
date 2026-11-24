---
version: 2
name: cross-cutting-serializeddagmodel-entity-compute-determ-a955
description: "SerializedDagModel Payload Hashing and Integrity Verification: Serializeddagmodel Entity Compute Deterministic Hash Digest"
metadata:
  actual:
    adr_id: a955829c-5939-4524-98b0-f4594ca33c15
    status: accepted
    candidate_group_id: 1e964b15-9872-41c3-836f-d17c05317f56
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# SerializedDagModel Payload Hashing and Integrity Verification: Serializeddagmodel Entity Compute Deterministic Hash Digest

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Serialization and persistence of workflow models containing structured task and dependency definitions.
- Version comparison and state synchronization between processing workers and persistent database storage.

### Rules

- **R-A-001** MUST: The SerializedDagModel entity MUST compute a deterministic hash digest of its serialized payload via SerializedDagModel.hash prior to persistence or version comparison.

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
