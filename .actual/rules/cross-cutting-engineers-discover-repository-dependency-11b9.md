---
version: 2
name: cross-cutting-engineers-discover-repository-dependency-11b9
description: "SQLAlchemy Declarative 2.0 Mapping with Mapped and mapped_column: Engineers Discover Repository Dependency Lock Artifact"
metadata:
  actual:
    adr_id: 11b971d9-2c55-4904-8e90-aae19d56ee4a
    status: accepted
    candidate_group_id: b1c541c8-c007-4d99-9240-e55b8d2231a7
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# SQLAlchemy Declarative 2.0 Mapping with Mapped and mapped_column: Engineers Discover Repository Dependency Lock Artifact

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All relational entity definitions representing state, metadata, and operational history in the metadata store.
- All cross-entity relational definitions and table constraints managed by the persistence framework.

### Rules

- **R-DM-001** MUST: Engineers MUST discover the repository dependency lock artifact and resolve the exact locked version of SQLAlchemy before writing or modifying ORM model definitions.

### Verify

- Discover the static type checker execution command from project configuration and run it against the model definition modules to verify that all Mapped column definitions satisfy type contracts.
- Discover the test runner command from the repository build configuration and execute the data modeling test suite to validate database schema migrations and ORM relationship constraints.
- Discover the code linter command from project configuration and run it to verify compliance with declarative ORM style conventions.

**Accept when:**

- Static type analysis passes without errors or mismatches on Mapped attribute declarations and relationship mappings.
- Database model test executions pass across supported relational dialects without constraint violations or schema generation errors.
- All new and modified persistent entities inherit from the declarative base and declare columns using mapped_column.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks verifying typed column annotations on all subclasses of the declarative base.
- Verified by: Automated schema migration tests running against all supported database dialect containers.
- Verified by: Peer review verification ensuring explicit relationship cascades and table argument constraints.
- Violation handling: Continuous integration failure on missing or improperly typed column declarations.
- Violation handling: Pull requests introducing untyped persistence models or missing table constraints will be blocked from merging.
- Violation handling: Flagged violations must be refactored to comply with typed declarative mapping rules before review sign-off.
- Exception process: Submit a technical justification detailing why a persistent entity cannot utilize the declarative base or mapped column syntax.
- Exception process: Obtain approval from the database architecture review team prior to merging custom schema structures.
- Exception process: Document dialect compatibility guarantees and lifecycle handling for any exempt persistence structure.
</enforcement>
