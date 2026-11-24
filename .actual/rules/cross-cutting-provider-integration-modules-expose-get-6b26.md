---
version: 2
name: cross-cutting-provider-integration-modules-expose-get-6b26
description: "Public Provider Integration Contract and packaging.version Compatibility Parsing: Provider Integration Modules Expose Get Info"
metadata:
  actual:
    adr_id: 6b262b2f-49c9-4a41-9e0c-85dd5a9e96d5
    status: accepted
    candidate_group_id: be490599-ed25-45b1-869a-0a8a3f622981
    evidence:
      source: deep
  scope:
    root: providers/apache/flink
    paths:
      - src/airflow/providers/apache/flink/**
---

# Public Provider Integration Contract and packaging.version Compatibility Parsing: Provider Integration Modules Expose Get Info

Scope: applies to `providers/apache/flink/src/airflow/providers/apache/flink/**`.

These rules are ALWAYS ACTIVE for:
- Provider integration packages publishing public contract interfaces.
- Modules performing runtime environment version verification.

### Rules

- **R-PEA-001** MUST: Provider integration modules MUST expose get_provider_info as their primary public capability contract.

### Verify

- Discover and run the project contract verification suite to ensure provider metadata matches expected schemas.
- Discover and run the compatibility test suite across supported runtime matrix targets.

**Accept when:**

- All public contract entry points return schemas conforming to provider specifications.
- Runtime version compatibility checks successfully parse environment versions using packaging.version.Version semantics.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated test suites validating contract schema adherence and version comparison matrix.
- Verified by: Pull request code review enforcing integration contract signatures.
- Violation handling: Build failure during automated continuous integration contract checks.
- Exception process: Exceptions require approval from framework core maintainers with a documented migration path.
</enforcement>
