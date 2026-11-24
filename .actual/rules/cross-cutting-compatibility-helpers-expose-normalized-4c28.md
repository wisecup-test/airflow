---
version: 2
name: cross-cutting-compatibility-helpers-expose-normalized-4c28
description: "Core Version Parsing and Normalization with packaging.version: Compatibility Helpers Expose Normalized Numeric Tuples"
metadata:
  actual:
    adr_id: 4c28e004-5886-4b34-94a9-34d674bbeded
    status: accepted
    candidate_group_id: c70d7b7e-886e-44b2-bf1c-2bf49a18609f
    evidence:
      source: deep
  scope:
    root: providers/apache/flink
    paths:
      - src/airflow/providers/apache/flink/**
---

# Core Version Parsing and Normalization with packaging.version: Compatibility Helpers Expose Normalized Numeric Tuples

Scope: applies to `providers/apache/flink/src/airflow/providers/apache/flink/**`.

These rules are ALWAYS ACTIVE for:
- Provider modules and extension packages that perform runtime feature detection against the core host framework.

### Rules

- **R-LM-001** SHOULD: Compatibility helpers SHOULD expose normalized numeric tuples rather than exposing raw Version instances directly across module interfaces.

### Verify

- Discover the project test runner script in the repository configuration and execute the unit test suite targeting compatibility modules.
- Inspect dependency manifests and repository lock files to confirm proper declaration of packaging primitives.

**Accept when:**

- Unit tests verify that get_base_airflow_version_tuple produces correct numeric tuples for standard, pre-release, and development version strings.
- Static analysis confirms that no raw lexical version string comparisons exist within provider integration modules.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites executed during continuous integration workflows.
- Verified by: Peer code review for all new and modified provider compatibility code.
- Violation handling: Pull requests containing raw string comparisons for version checking will be blocked until refactored to use the standardized compatibility contract.
- Exception process: Exceptions require review and approval by integration architects when interacting with upstream systems that do not follow standard semantic versioning.
</enforcement>
