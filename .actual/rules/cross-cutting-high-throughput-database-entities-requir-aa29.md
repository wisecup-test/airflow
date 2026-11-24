---
version: 2
name: cross-cutting-high-throughput-database-entities-requir-aa29
description: "Adopt uuid Module for Universal Entity Identification Across Persistence and API Boundaries: High Throughput Database Entities Requiring Sequential"
metadata:
  actual:
    adr_id: aa29083f-e04a-4323-976b-dd0c5da11650
    status: accepted
    candidate_group_id: ff5dcc5e-8f7d-43df-bfc7-5e5ae3c9a01e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adopt uuid Module for Universal Entity Identification Across Persistence and API Boundaries: High Throughput Database Entities Requiring Sequential

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Surrogate primary keys and foreign key definitions within database entity models.
- Identifier parameters declared in REST API route handlers.
- Entity representation fields in request and response serialization schemas.
- Identity subjects and resource references in execution authentication tokens.

### Rules

- **R-LM-001** SHOULD: High-throughput database entities requiring sequential index locality SHOULD generate time-ordered identifiers using uuid6.uuid7.

### Verify

- Discover the project test runner and execute the automated test suite covering data model persistence and API schema serialization.
- Discover the repository static type analysis tool and run it across data models and route definitions to verify UUID typing compliance.

**Accept when:**

- All entity identifier attributes in data models and serialization schemas pass type checking with UUID typing.
- API route handlers validate input identifier path parameters as UUID instances and serialize them to standard UUID string representations.
- Database column specifications for primary and foreign entity keys use native UUID mappings without unvalidated string fallbacks.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static type analysis integrated into the continuous integration pipeline to verify UUID typing.
- Verified by: Schema validation checks confirming identifier column definitions in database migration manifests.
- Verified by: Code review verification ensuring route parameters and response schemas declare explicit UUID types.
- Violation handling: Automated pull request check failures when entity identifiers are declared as untyped values or generic strings.
- Violation handling: Rejection of database migrations that introduce auto-incrementing integer surrogate keys for core domain entities.
- Exception process: Submit an architectural exception request detailing the technical or external integration constraint requiring an alternative identifier structure.
- Exception process: Obtain approval from the technical architecture review team before committing non-UUID entity identifiers.
- Exception process: Document the non-standard identifier rationale and validation mapping directly within the module documentation.
</enforcement>
