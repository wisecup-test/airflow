---
version: 2
name: cross-cutting-versionchange-classes-declare-backward-m-8c0c
description: "Adopt Cadwyn for API Versioning and Schema Migrations: Versionchange Classes Declare Backward Migration Directives"
metadata:
  actual:
    adr_id: 8c0c8e7b-94e0-48d2-9165-79fd517b9df4
    status: accepted
    candidate_group_id: 7d1d5e64-ead0-46c0-97ec-af4694a11090
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/execution_api/**
---

# Adopt Cadwyn for API Versioning and Schema Migrations: Versionchange Classes Declare Backward Migration Directives

Scope: applies to `airflow-core/src/airflow/api_fastapi/execution_api/**`.

These rules are ALWAYS ACTIVE for:
- All execution API route definitions, schemas, and version migration modules.
- Modifications to client-facing HTTP payloads and endpoint availability across API releases.

### Rules

- **R-LM-001** MUST: VersionChange classes MUST declare backward migration directives using instructions_to_migrate_to_previous_version containing schema and endpoint migration specifications.

### Verify

- Discover and execute the project API test suite targeting execution API version migration pathways.
- Run the project contract verification and schema validation procedures to confirm version migration integrity across all declared releases.

**Accept when:**

- All automated test suites covering API version migrations and backward-compatibility payload transformations pass without error.
- All registered version changes successfully transform contemporary schemas and response payloads into legacy formats across every historical API release.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites validating backward compatibility conversions against prior version schemas.
- Verified by: Pull request code reviews ensuring new or modified endpoints define corresponding version migration classes.
- Violation handling: Pull requests introducing schema changes or endpoint modifications without required migration definitions are blocked in continuous integration.
- Violation handling: Code review rejection for route handlers containing ad-hoc manual version conditional checks.
- Exception process: Exceptions to version migration structures require review and formal approval from the API architecture maintainers.
- Exception process: Any bypass of backward compatibility guarantees must be documented alongside an explicit client deprecation notice.
</enforcement>
