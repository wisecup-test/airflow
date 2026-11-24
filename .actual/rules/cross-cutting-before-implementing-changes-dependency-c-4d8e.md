---
version: 2
name: cross-cutting-before-implementing-changes-dependency-c-4d8e
description: "Standard Library JSON Module Serialization Adoption: Before Implementing Changes Dependency Configurations Developers"
metadata:
  actual:
    adr_id: 4d8ec6b4-960b-4039-87c3-4fb83f403438
    status: accepted
    candidate_group_id: 8eb0174e-c423-4bbb-ad76-ef4420377eca
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Standard Library JSON Module Serialization Adoption: Before Implementing Changes Dependency Configurations Developers

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Serialization and deserialization of runtime configuration objects, credentials, and authentication state.
- Inter-process payload exchange across execution API endpoints, CLI command interfaces, and task instances.
- Storage and retrieval of serialized asset profile identifiers and workflow parameters.

### Rules

- **R-LM-001** MUST: Before implementing changes to dependency configurations, developers MUST discover the project dependency lock artifact and verify the exact resolved environment specification.

### Verify

- Discover and run the project static analysis suite to check for compliant json module usage and error handling.
- Discover and execute the project test runner across serialization and API test suites to verify payload encoding and decoding behavior.

**Accept when:**

- All serialization and deserialization routines execute without unhandled decoding errors across test suites.
- Static analysis validates that no unsafe deserialization functions are introduced at external or inter-process boundaries.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks executing static analysis and test validation suites.
- Verified by: Mandatory peer code review verifying adherence to serialization rules and error handling conventions.
- Violation handling: Pull requests introducing unhandled deserialization calls or unapproved serializers are blocked until corrected.
- Exception process: Exceptions must be formally submitted to the architecture review board with performance benchmarks demonstrating necessity.
</enforcement>
