---
version: 2
name: cross-cutting-database-synchronization-handlers-compar-680f
description: "SerializedDagModel Payload Hashing and Integrity Verification: Database Synchronization Handlers Compare Newly Calculated"
metadata:
  actual:
    adr_id: 680f3df1-b4c8-4748-ab17-856992c6dafc
    status: accepted
    candidate_group_id: 1e964b15-9872-41c3-836f-d17c05317f56
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# SerializedDagModel Payload Hashing and Integrity Verification: Database Synchronization Handlers Compare Newly Calculated

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Serialization and persistence of workflow models containing structured task and dependency definitions.
- Version comparison and state synchronization between processing workers and persistent database storage.

### Rules

- **R-A-001** SHOULD: Database synchronization handlers SHOULD compare newly calculated payload digests against prefetched model digests before issuing update statements or creating new version records.

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
