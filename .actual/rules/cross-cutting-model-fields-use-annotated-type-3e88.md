---
version: 2
name: cross-cutting-model-fields-use-annotated-type-3e88
description: "Pydantic Schema Validation for API Data Models: Model Fields Use Annotated Type Declarations"
metadata:
  actual:
    adr_id: 3e8833f8-8c01-407a-84cd-c1f1ccec3b08
    status: accepted
    candidate_group_id: a577c1aa-7504-47e2-9d05-56f68d2a89a1
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/api/datamodels/**
---

# Pydantic Schema Validation for API Data Models: Model Fields Use Annotated Type Declarations

Scope: applies to `airflow-ctl/src/airflowctl/api/datamodels/**`.

These rules are ALWAYS ACTIVE for:
- Public API request data models, ingress mutation payloads, and domain boundary response serializers.

### Rules

- **R-BRE-001** MUST: Model fields MUST use Annotated type declarations with Field metadata to establish explicit schema constraints.

### Verify

- Discover and execute the repository's test runner to verify data model validation behavior against valid and invalid payloads.
- Discover and execute the project's static type analysis suite to verify type annotations across all data model definitions.

**Accept when:**

- All data model contracts inherit from Pydantic BaseModel and pass schema validation tests.
- Request body models enforce strict extra field rejection through configuration.
- Static analysis verifies complete type annotation and Field metadata coverage across all model definitions.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites executing schema validation assertions against valid and invalid payloads.
- Verified by: Static type checking and linting rules enforced within the continuous integration pipeline.
- Verified by: Architecture and code review inspection of data model definitions.
- Violation handling: Continuous integration checks fail when models bypass BaseModel inheritance or omit strict configuration on ingress schemas.
- Violation handling: Pull requests introducing unvalidated data structures are blocked until brought into compliance.
- Exception process: Submit an architectural review request documenting why dynamic or unvalidated payload structures are required.
- Exception process: Require sign-off from domain leads before permitting unvalidated input models.
</enforcement>
