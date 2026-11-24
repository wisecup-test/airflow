---
version: 2
name: cross-cutting-components-parsing-string-inputs-via-6f77
description: "Standard Library JSON Module Serialization Adoption: Components Parsing String Inputs Via Json"
metadata:
  actual:
    adr_id: 6f777740-550e-4c26-9674-74ccc00142c0
    status: accepted
    candidate_group_id: 8eb0174e-c423-4bbb-ad76-ef4420377eca
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Standard Library JSON Module Serialization Adoption: Components Parsing String Inputs Via Json

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- Serialization and deserialization of runtime configuration objects, credentials, and authentication state.
- Inter-process payload exchange across execution API endpoints, CLI command interfaces, and task instances.
- Storage and retrieval of serialized asset profile identifiers and workflow parameters.

### Rules

- **R-LM-001** MUST: Components parsing string inputs via json.loads MUST handle decoding failures explicitly and convert parsing errors into domain-specific exception types or client error statuses.

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
