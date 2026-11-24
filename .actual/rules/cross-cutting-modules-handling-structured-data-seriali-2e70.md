---
version: 2
name: cross-cutting-modules-handling-structured-data-seriali-2e70
description: "Adoption of json Module for Structured Data Serialization and Deserialization: Modules Handling Structured Data Serialization Deserialization"
metadata:
  actual:
    adr_id: 2e70d309-985a-4688-b200-5223ba72de54
    status: accepted
    candidate_group_id: af44b0b4-c9c6-4d63-8a26-b076a0921fdd
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
      - ide_setup/**
      - registry/**
      - stats/**
---

# Adoption of json Module for Structured Data Serialization and Deserialization: Modules Handling Structured Data Serialization Deserialization

Scope: applies to `dev/breeze/src/airflow_breeze/**`, `dev/ide_setup/**`, `dev/registry/**`, `dev/stats/**`.

These rules are ALWAYS ACTIVE for:
- All repository developer tooling, registry generators, automation commands, and metadata extraction scripts.
- Inter-process payload communication and serialization of structured configuration artifacts.

### Rules

- **R-LM-001** MUST: Modules handling structured data serialization or deserialization across toolchain boundaries MUST use the standard json module APIs, utilizing json.load for file streams and json.loads for in-memory string payloads.

### Verify

- Discover and execute the repository static analysis test suite to confirm compliance with allowed serialization library imports.
- Discover and run the project test execution runner targeting tooling and registry test suites to verify serialization behavior.

**Accept when:**

- Automated linting and static analysis checks pass with zero syntax or dependency errors across all toolchain modules.
- Integration verification scripts successfully serialize and deserialize metadata artifacts using the core serialization library without schema degradation.
- All newly introduced data interchange routines demonstrate test coverage validating key retrieval and defensive fallback behavior.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks enforcing allowed library imports within tooling and command directories.
- Verified by: Continuous integration test suites verifying data exchange, serialization, and deserialization routines.
- Verified by: Peer review on pull requests touching metadata generation, serialization contracts, or inter-module data transfer.
- Violation handling: Pull requests introducing unauthorized external serialization packages without an approved exception will fail automated validation.
- Violation handling: Code using unsafe deserialization patterns without defensive access or error handling will be blocked during code review.
- Exception process: Proposals to adopt alternative serialization libraries must be submitted to the technical steering group with documented throughput benchmarks and schema justification.
- Exception process: Temporary exceptions require architectural approval and an issue tracker entry detailing the migration plan.
</enforcement>
