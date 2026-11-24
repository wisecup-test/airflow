---
version: 2
name: cross-cutting-hash-generation-routine-recursively-sort-4f71
description: "SerializedDagModel Payload Hashing and Integrity Verification: Hash Generation Routine Recursively Sort Dictionary"
metadata:
  actual:
    adr_id: 4f717e08-9c69-4945-a730-688d67a95c40
    status: accepted
    candidate_group_id: 1e964b15-9872-41c3-836f-d17c05317f56
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# SerializedDagModel Payload Hashing and Integrity Verification: Hash Generation Routine Recursively Sort Dictionary

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Serialization and persistence of workflow models containing structured task and dependency definitions.
- Version comparison and state synchronization between processing workers and persistent database storage.

### Rules

- **R-A-001** MUST: The hash generation routine MUST recursively sort dictionary keys and nested task identifiers while removing non-semantic execution location metadata prior to computing the digest.

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
