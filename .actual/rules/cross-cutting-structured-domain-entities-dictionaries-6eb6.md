---
version: 2
name: cross-cutting-structured-domain-entities-dictionaries-6eb6
description: "Standard Library JSON Module Serialization Adoption: Structured Domain Entities Dictionaries Intended Persistence"
metadata:
  actual:
    adr_id: 6eb66f66-56f1-4663-a3e8-b9c67fbada9f
    status: accepted
    candidate_group_id: 8eb0174e-c423-4bbb-ad76-ef4420377eca
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Standard Library JSON Module Serialization Adoption: Structured Domain Entities Dictionaries Intended Persistence

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Serialization and deserialization of runtime configuration objects, credentials, and authentication state.
- Inter-process payload exchange across execution API endpoints, CLI command interfaces, and task instances.
- Storage and retrieval of serialized asset profile identifiers and workflow parameters.

### Rules

- **R-LM-001** MUST: Structured domain entities and dictionaries intended for persistence or network transport MUST be formatted using json.dumps to maintain standardized serialization contracts.

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
