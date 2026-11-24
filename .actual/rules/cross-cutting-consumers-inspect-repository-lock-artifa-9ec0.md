---
version: 2
name: cross-cutting-consumers-inspect-repository-lock-artifa-9ec0
description: "packaging.version Adoption for Runtime Framework Version Compatibility: Consumers Inspect Repository Lock Artifact Determine"
metadata:
  actual:
    adr_id: 9ec03caa-b01a-4941-9855-62b0026a7ae6
    status: accepted
    candidate_group_id: 13463ee2-7989-4102-8c57-c8c0d3ea36c9
    evidence:
      source: deep
  scope:
    root: providers/apache/beam
    paths:
      - src/airflow/providers/apache/beam/**
---

# packaging.version Adoption for Runtime Framework Version Compatibility: Consumers Inspect Repository Lock Artifact Determine

Scope: applies to `providers/apache/beam/src/airflow/providers/apache/beam/**`.

These rules are ALWAYS ACTIVE for:
- Integration modules and provider packages that conditionally branch based on the runtime host framework version.
- Modules defining compatibility shims or feature-support contracts across host framework releases.

### Rules

- **R-LM-001** MUST: Consumers MUST inspect the repository lock artifact to determine and verify the exact resolved version of packaging.version prior to implementing compatibility checks.

### Verify

- Discover and run the test suite for compatibility modules using the project test runner.
- Execute the repository linter and static type checker across integration provider modules to verify API contract compliance.

**Accept when:**

- Version tuple generation accurately reflects runtime host framework releases across all configured test matrices.
- Static analysis checks pass with zero errors against declared version compatibility contracts.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration test matrices testing across supported host framework releases.
- Verified by: Static analysis and code review checking for unauthorized manual version string parsing.
- Violation handling: Automated continuous integration failure when version comparison rules are violated or unapproved string parsing is detected.
- Violation handling: Code review rejection for any pull request that bypasses centralized version compatibility helpers.
- Exception process: Submit an architectural deviation request detailing why standard version tuple contracts are insufficient for review by maintainers.
</enforcement>
