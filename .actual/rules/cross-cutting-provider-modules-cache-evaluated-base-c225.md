---
version: 2
name: cross-cutting-provider-modules-cache-evaluated-base-c225
description: "Core Version Parsing and Normalization with packaging.version: Provider Modules Cache Evaluated Base Version"
metadata:
  actual:
    adr_id: c2252af7-e38c-441e-8c65-2e0f672abbaa
    status: accepted
    candidate_group_id: c70d7b7e-886e-44b2-bf1c-2bf49a18609f
    evidence:
      source: deep
  scope:
    root: providers/apache/flink
    paths:
      - src/airflow/providers/apache/flink/**
---

# Core Version Parsing and Normalization with packaging.version: Provider Modules Cache Evaluated Base Version

Scope: applies to `providers/apache/flink/src/airflow/providers/apache/flink/**`.

These rules are ALWAYS ACTIVE for:
- Provider modules and extension packages that perform runtime feature detection against the core host framework.

### Rules

- **R-LM-001** MAY: Provider modules MAY cache the evaluated base version tuple in module scope to prevent redundant parsing during repetitive task execution.

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
