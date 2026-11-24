---
version: 2
name: cross-cutting-components-exposing-serializing-transfer-910c
description: "Standardization of Asset Contracts on airflow.api_fastapi.execution_api.datamodels.asset Module: Components Exposing Serializing Transferring Asset Data"
metadata:
  actual:
    adr_id: 910cc860-b48b-4aba-ba3f-bd7a556ff711
    status: accepted
    candidate_group_id: 1b87d68b-f00a-469a-8b13-a03dc3ce8547
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Standardization of Asset Contracts on airflow.api_fastapi.execution_api.datamodels.asset Module: Components Exposing Serializing Transferring Asset Data

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Execution API endpoints handling asset retrieval by name, URI, or alias.
- Serialization definition classes generating unique keys and profiles for asset entities.
- Runtime event datamodels transmitting asset state changes and associated run references.

### Rules

- **R-LM-001** MUST: Components exposing, serializing, or transferring asset data across execution API boundaries MUST standardize on the canonical schema definitions provided by airflow.api_fastapi.execution_api.datamodels.asset.

### Verify

- Discover the repository test runner from the project manifest and execute the test suites covering execution API routes, serialization definitions, and asset event datamodels.
- Discover and run the project static analysis and type checking utilities to verify schema compliance and type integrity across all asset datamodel consumers.

**Accept when:**

- All execution API route handlers returning asset data validate domain records into AssetResponse models from the canonical asset datamodels module.
- Serialization definition classes and runtime event response schemas reference canonical asset data models without defining divergent duplicate schemas.
- Discovered automated test suites and static type verification checks across affected components execute without schema validation or type mismatches.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static type analysis and contract validation runs in continuous integration pipelines.
- Verified by: Mandatory peer code reviews on changes affecting execution API routes, event schemas, or serialization definitions.
- Violation handling: Automated build failure when routes or serialization modules introduce unapproved ad-hoc schemas for asset entities.
- Violation handling: Rejection of pull requests that bypass canonical asset model validation in execution boundary endpoints.
- Exception process: Submit an architectural review request outlining technical blockers that prevent direct adoption of the canonical asset datamodels module.
- Exception process: Obtain explicit sign-off from the core architecture maintainers prior to merging divergent schema definitions.
</enforcement>
