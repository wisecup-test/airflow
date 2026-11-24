---
version: 2
name: cross-cutting-endpoints-designed-exclusively-support-l-f5d5
description: "Adopt Cadwyn for API Versioning and Schema Migrations: Endpoints Designed Exclusively Support Legacy Compatibility"
metadata:
  actual:
    adr_id: f5d57bb2-680a-45b8-bd4b-b355f3cd51c5
    status: accepted
    candidate_group_id: 7d1d5e64-ead0-46c0-97ec-af4694a11090
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/execution_api/**
---

# Adopt Cadwyn for API Versioning and Schema Migrations: Endpoints Designed Exclusively Support Legacy Compatibility

Scope: applies to `airflow-core/src/airflow/api_fastapi/execution_api/**`.

These rules are ALWAYS ACTIVE for:
- All execution API route definitions, schemas, and version migration modules.
- Modifications to client-facing HTTP payloads and endpoint availability across API releases.

### Rules

- **R-LM-001** MUST: Endpoints designed exclusively to support legacy compatibility MUST be explicitly annotated with only_exists_in_older_versions decorators on router declarations to prevent modern clients from querying legacy routes.

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
