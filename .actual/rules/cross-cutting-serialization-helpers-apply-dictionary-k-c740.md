---
version: 2
name: cross-cutting-serialization-helpers-apply-dictionary-k-c740
description: "Adoption of airflow._shared.secrets_masker for Centralized Secret Redaction: Serialization Helpers Apply Dictionary Key Normalization"
metadata:
  actual:
    adr_id: c74093be-d432-4133-a88b-545f712254c8
    status: accepted
    candidate_group_id: 0c1cc8ab-f614-448b-8d9a-f20496e63e77
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of airflow._shared.secrets_masker for Centralized Secret Redaction: Serialization Helpers Apply Dictionary Key Normalization

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- API response schemas exposing entity metadata, extra configurations, and environment or variable values
- Core serialization helpers handling dictionary normalization and template field serialization
- Public API service operations performing bulk extraction or modification of connection credentials

### Rules

- **R-LM-001** MAY: Serialization helpers MAY apply dictionary key normalization and canonical ordering in conjunction with redact to ensure deterministic output for masked data structures.

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
