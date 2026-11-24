---
version: 2
name: cross-cutting-schema-field-additions-removals-type-c147
description: "Cadwyn API Versioning Framework Adoption: Schema Field Additions Removals Type Modifications"
metadata:
  actual:
    adr_id: c147057f-119b-4ef9-b0ca-8e35f24547e7
    status: accepted
    candidate_group_id: f3015b06-763b-4003-8767-ca8a62bfc415
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Cadwyn API Versioning Framework Adoption: Schema Field Additions Removals Type Modifications

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Execution API route definitions, schema models, and response serialization contracts.
- Modifications to public API payloads that require backward compatibility across deployed worker components.

### Rules

- **R-LM-001** MUST: Schema field additions, removals, or type modifications across versions MUST be declared using schema mutation instructions within the migration tuple.

### Verify

- Discover the test runner invocation from the project manifest and execute the test suite for API version change schemas.
- Discover the static analysis scripts from repository configuration and run verification across all migration modules.
- Discover the contract verification suite to validate backward-compatibility transformations against previous client schemas.

**Accept when:**

- All declared VersionChange classes transform models consistently between the latest schema and designated previous version contracts.
- Verification suites confirm response downgrade functions generate expected payload structures without runtime transformation errors.
- Static verification confirms that all modified public API routes maintain corresponding migration instructions.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated contract test suites verifying schema conversions across each declared version.
- Verified by: Architecture review during pull requests affecting execution API models and endpoints.
- Violation handling: Pull requests altering public schemas without corresponding VersionChange declarations are blocked during integration verification.
- Exception process: Submit an architectural review request with justification detailing why a hard breaking change is necessary, requiring approval from the API architecture leads.
</enforcement>
