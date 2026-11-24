---
version: 2
name: cross-cutting-data-models-response-serializers-seriali-fb5c
description: "Adoption of airflow._shared.secrets_masker for Centralized Secret Redaction: Data Models Response Serializers Serialization Helpers"
metadata:
  actual:
    adr_id: fb5c3ddb-7a4f-4411-9b2e-38b003b7cd7e
    status: accepted
    candidate_group_id: 0c1cc8ab-f614-448b-8d9a-f20496e63e77
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of airflow._shared.secrets_masker for Centralized Secret Redaction: Data Models Response Serializers Serialization Helpers

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- API response schemas exposing entity metadata, extra configurations, and environment or variable values
- Core serialization helpers handling dictionary normalization and template field serialization
- Public API service operations performing bulk extraction or modification of connection credentials

### Rules

- **R-LM-001** MUST: All data models, response serializers, and serialization helpers that expose or persist structures containing user-supplied configuration, credentials, or arbitrary metadata MUST invoke the redact function from airflow._shared.secrets_masker on candidate payload fields prior to external serialization.

### Verify

- Discover the test runner from the project manifest and execute the test suite targeting data model validation and secret masking routines.
- Discover and run the project static analysis and linting scripts to verify compliance with shared masking import constraints.

**Accept when:**

- All automated test suites for API response models and serialization helpers pass without exposing unredacted sensitive values.
- Static analysis validates that all sensitive field serialization paths import and execute the centralized masking function.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites validating payload redaction in API response models and serialization outputs.
- Verified by: Static analysis and peer code reviews ensuring all newly created response models and serialization routines adopt the shared masker.
- Violation handling: Pull requests exposing unredacted sensitive fields or bypassing the shared masking module are blocked in code review and continuous integration checks.
- Exception process: Exceptions must be submitted via an architecture review request detailing why raw credentials must be transmitted, subject to explicit security lead approval.
</enforcement>
