---
version: 2
name: cross-cutting-command-option-builders-parameter-parsin-8b8d
description: "Centralized Domain Constants Module Adoption with airflow_breeze.global_constants: Command Option Builders Parameter Parsing Structures"
metadata:
  actual:
    adr_id: 8b8d6f60-15c7-4ec7-82e5-bc04828ac236
    status: accepted
    candidate_group_id: ddff902e-4340-4aec-a74e-404896994ed8
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Centralized Domain Constants Module Adoption with airflow_breeze.global_constants: Command Option Builders Parameter Parsing Structures

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- All developer tooling commands, parameter mapping classes, and utility modules within the tooling package that reference shared choice enumerations, allowed platform versions, or build cache paths.

### Rules

- **R-LM-001** SHOULD: Command option builders and parameter parsing structures SHOULD wrap collection constants imported from airflow_breeze.global_constants inside dedicated choice validation wrappers to guarantee user input matches authorized values.

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
