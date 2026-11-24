---
version: 2
name: cross-cutting-before-introducing-modifying-runtime-lib-5eec
description: "Centralized Domain Constants Module Adoption with airflow_breeze.global_constants: Before Introducing Modifying Runtime Library Dependencies"
metadata:
  actual:
    adr_id: 5eeccc42-be14-4f5a-aa66-8d0a1a98fbe0
    status: accepted
    candidate_group_id: ddff902e-4340-4aec-a74e-404896994ed8
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Centralized Domain Constants Module Adoption with airflow_breeze.global_constants: Before Introducing Modifying Runtime Library Dependencies

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- All developer tooling commands, parameter mapping classes, and utility modules within the tooling package that reference shared choice enumerations, allowed platform versions, or build cache paths.

### Rules

- **R-LM-001** MUST: Before introducing or modifying runtime library dependencies associated with constant definitions, developers MUST inspect the repository lock artifact to verify the exact resolved version.

### Verify

- test -n "$(find . -type f -name "*.py" -exec grep -l "airflow_breeze.global_constants" {} +)" && echo "Central constants references verified"
- # Discover and execute repository test suite for developer tooling
- # Discover and execute repository linting suite for import enforcement

**Accept when:**

- All references to shared configuration choices and build constants import directly from airflow_breeze.global_constants.
- No duplicate local literal definitions of shared constant sets exist across tooling command and parameter modules.
- The discovered project test and linting verification suites execute successfully with zero violations.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks executing import linting and static type analysis.
- Verified by: Peer code review verification on pull requests modifying developer tooling options or parameters.
- Violation handling: Pull requests introducing local duplicate constant sets or bypassing the centralized constants module will fail continuous integration validation.
- Violation handling: Code review rejections requiring contributors to consolidate duplicated values into airflow_breeze.global_constants before merge.
- Exception process: Submit an exception request to the tooling architecture maintainers documenting the necessity for local scoping or experimental divergence.
- Exception process: Upon approval, document the exception with inline justification as specified in the policy exception block.
</enforcement>
