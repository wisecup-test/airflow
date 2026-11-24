---
version: 2
name: cross-cutting-data-transfer-models-request-bodies-507c
description: "Pydantic BaseModel and ConfigDict for API Data Modeling: Data Transfer Models Request Bodies Response"
metadata:
  actual:
    adr_id: 507cfb37-fc88-4a81-9fd8-8760d2f2644e
    status: accepted
    candidate_group_id: 570a3ac2-93ef-4f54-a90e-5d29f549bf69
    evidence:
      source: deep
  scope:
    root: airflow-ctl
    paths:
      - src/airflowctl/api/datamodels/**
---

# Pydantic BaseModel and ConfigDict for API Data Modeling: Data Transfer Models Request Bodies Response

Scope: applies to `airflow-ctl/src/airflowctl/api/datamodels/**`.

These rules are ALWAYS ACTIVE for:
- All public API data transfer objects, request bodies, response models, and error response schemas.

### Rules

- **R-LM-001** MUST: All API data transfer models, request bodies, and response serializers MUST inherit from BaseModel to enforce declarative contract schemas.

### Verify

- Discover and run the project static type checker to verify that all data transfer models satisfy model and field typing constraints.
- Discover and execute the repository unit test suite to ensure all serialization and deserialization validation rules pass.

**Accept when:**

- All data transfer models inherit from BaseModel and define model attributes using Annotated and Field metadata.
- Inbound mutation models explicitly configure forbidden undeclared attributes via ConfigDict.
- Type check and test execution passes across the data model test suites.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static type checking and schema contract validation tests executed in continuous integration.
- Verified by: Pull request peer review validating schema configurations on new or updated endpoint models.
- Violation handling: Static analysis or continuous integration build failures block merges for unvalidated models or missing lock verification.
- Violation handling: Non-compliant models must be refactored to inherit from BaseModel with explicit Field metadata.
- Exception process: Submit an architectural review request with justification detailing why standard model validation is infeasible for the specific payload boundary.
- Exception process: Obtain sign-off from API engineering leads prior to deploying unvalidated payload parsers.
</enforcement>
