---
version: 2
name: cross-cutting-engineers-discover-project-dependency-lo-491c
description: "Adoption of collections.abc for Container Protocols and Interface Typing: Engineers Discover Project Dependency Lock Artifact"
metadata:
  actual:
    adr_id: 491cd0ff-bcec-4199-88e2-41c59891a17f
    status: accepted
    candidate_group_id: 76cf2bee-4161-45f8-a33b-5b27b528a00c
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - hatch_build.py
      - src/airflow/**
---

# Adoption of collections.abc for Container Protocols and Interface Typing: Engineers Discover Project Dependency Lock Artifact

Scope: applies to `airflow-core/hatch_build.py`, `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All public API route definitions, request bodies, and response serialization models.
- All core execution workloads, state stores, and task orchestration interfaces exchanging collections of domain objects.
- Runtime type introspection guards that verify collection and mapping behaviors.

### Rules

- **R-LM-001** MUST: Engineers MUST discover the project dependency lock artifact and verify runtime environment compatibility before adopting or extending module dependency interfaces.

### Verify

- Discover and execute the project static type analysis test suites to verify that all container annotations adhere to abstract container protocols.
- Discover and execute the project contract test suites to verify serialization and runtime validation of abstract collection payloads.

**Accept when:**

- Static type analysis passes across all public modules, data models, and API interfaces without container type mismatches.
- Runtime test suites pass when receiving custom iterable or mapping implementations adhering to collections.abc protocols.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static type analysis in the continuous integration pipeline verifying that signatures use abstract container protocols.
- Verified by: Peer code review ensuring new public API models and internal contracts utilize collections.abc interfaces.
- Violation handling: Continuous integration job failure for non-compliant container annotations or concrete runtime type assertions.
- Violation handling: Rejection of pull requests introducing concrete collection constraints on public or cross-boundary interfaces.
- Exception process: Submit an architectural exception request detailing technical necessity and profiling data justifying concrete container coupling.
- Exception process: Obtain formal review and sign-off from the technical steering group before merging exceptions.
</enforcement>
