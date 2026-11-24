---
version: 2
name: cross-cutting-attributes-requiring-mutable-default-val-ff32
description: "Pydantic BaseModel for Core API UI Response Data Models: Attributes Requiring Mutable Default Values Use"
metadata:
  actual:
    adr_id: ff3215e5-e454-4176-b67d-4bdf37060783
    status: accepted
    candidate_group_id: 645baf61-da8f-40a6-8769-d3d167629679
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/api_fastapi/core_api/datamodels/ui/**
---

# Pydantic BaseModel for Core API UI Response Data Models: Attributes Requiring Mutable Default Values Use

Scope: applies to `airflow-core/src/airflow/api_fastapi/core_api/datamodels/ui/**`.

These rules are ALWAYS ACTIVE for:
- API endpoints serving web user interfaces
- Response contract definitions within the API data model layer

### Rules

- **R-LM-001** MUST: Attributes requiring mutable default values MUST use Pydantic Field with default factory declarations rather than mutable instances.

### Verify

- eval "$(discover_test_runner) $(discover_contract_test_suite)"
- eval "$(discover_type_checker) $(discover_datamodel_paths)"

**Accept when:**

- All data models defined for UI endpoints inherit from BaseModel and pass strict static type analysis.
- API contract test suites pass with zero schema validation failures or serialization errors.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated contract testing and static analysis in continuous integration pipelines.
- Verified by: Mandatory peer review on pull requests modifying API response schemas.
- Violation handling: Pull requests containing untyped response structures or missing BaseModel inheritance will fail continuous integration checks and be blocked from merge.
- Exception process: Exceptions require documented justification submitted to the architecture review team and explicit approval from the API platform lead.
</enforcement>
