---
version: 2
name: cross-cutting-provider-modules-centralize-version-comp-b4ef
description: "packaging.version Adoption for Runtime Framework Version Compatibility: Provider Modules Centralize Version Compatibility Logic"
metadata:
  actual:
    adr_id: b4ef6937-dbff-4f60-86ba-9368502a41c5
    status: accepted
    candidate_group_id: 13463ee2-7989-4102-8c57-c8c0d3ea36c9
    evidence:
      source: deep
  scope:
    root: providers/apache/beam
    paths:
      - src/airflow/providers/apache/beam/**
---

# packaging.version Adoption for Runtime Framework Version Compatibility: Provider Modules Centralize Version Compatibility Logic

Scope: applies to `providers/apache/beam/src/airflow/providers/apache/beam/**`.

These rules are ALWAYS ACTIVE for:
- Integration modules and provider packages that conditionally branch based on the runtime host framework version.
- Modules defining compatibility shims or feature-support contracts across host framework releases.

### Rules

- **R-LM-001** SHOULD: Provider modules SHOULD centralize version compatibility logic in dedicated compatibility modules rather than scattering version inspections across individual integration components.

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
