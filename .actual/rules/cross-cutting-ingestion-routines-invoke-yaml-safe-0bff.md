---
version: 2
name: cross-cutting-ingestion-routines-invoke-yaml-safe-0bff
description: "yaml Library Adoption for Structured Metadata and Configuration Ingestion: Ingestion Routines Invoke Yaml Safe Load"
metadata:
  actual:
    adr_id: 0bff357d-ed9d-48b2-b7a7-81561af9f46f
    status: accepted
    candidate_group_id: a52f5af7-a290-4157-a662-cea7093b1cdb
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/**
---

# yaml Library Adoption for Structured Metadata and Configuration Ingestion: Ingestion Routines Invoke Yaml Safe Load

Scope: applies to `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/**`.

These rules are ALWAYS ACTIVE for:
- Developer tooling, documentation build extensions, and test utilities that ingest structured configuration and metadata files.
- Infrastructure helper modules responsible for parsing declarative package metadata and environment configurations.

### Rules

- **R-LM-001** MUST: Ingestion routines MUST invoke yaml.safe_load when parsing text or file streams representing declarative metadata.

### Verify

- Discover the project static analysis script in the repository configuration and execute it to verify that no unsafe deserialization calls are present in the codebase.
- Discover and execute the repository test runner script to validate that configuration parsing utilities correctly deserialize structured metadata files.
- Locate the repository dependency validation script and execute it to confirm that all installed module versions match the resolved lock artifact.

**Accept when:**

- Static analysis passes across all internal utility and test modules with zero detected references to unsafe YAML deserialization interfaces.
- All automated tests covering configuration ingestion, documentation extensions, and test utilities pass successfully.
- All library imports match the declared and resolved dependency specifications in the repository lock artifact.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks in the continuous integration pipeline scanning for prohibited loader functions.
- Verified by: Peer code review for all modifications touching developer tooling, documentation extensions, or test infrastructure.
- Violation handling: Pull requests containing unsafe deserialization patterns or unverified library usages are blocked from merging until corrected.
- Violation handling: Security alerts are automatically triggered if unsafe loading patterns are introduced.
- Exception process: Exceptions require documented justification submitted via an architectural review issue and explicit approval from repository maintainers.
</enforcement>
