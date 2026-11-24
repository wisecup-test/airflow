---
version: 2
name: cross-cutting-engineers-inspect-authoritative-dependen-3ea2
description: "Adoption of airflow.sdk for Workflow Task Orchestration: Engineers Inspect Authoritative Dependency Lock Artifact"
metadata:
  actual:
    adr_id: 3ea2cbab-8425-4c21-9271-33f012b6474f
    status: accepted
    candidate_group_id: b96b8b42-63af-4816-aa21-0b9027376d89
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/dags/**
---

# Adoption of airflow.sdk for Workflow Task Orchestration: Engineers Inspect Authoritative Dependency Lock Artifact

Scope: applies to `dev/airflow_perf/dags/**`.

These rules are ALWAYS ACTIVE for:
- Workflow and directed acyclic graph definitions declared in the repository.
- Workload pipeline modules requiring task orchestration and dependency chaining.

### Rules

- **R-LM-001** MUST: Engineers MUST inspect the authoritative dependency lock artifact in the repository to determine the exact resolved version of the library before implementing or calling any module APIs.

### Verify

- Discover and run the project test runner to execute workflow validation suites verifying DAG structure and task dependency graphs.
- Discover and run the repository static analysis linter to confirm adherence to approved module import paths and SDK conventions.

**Accept when:**

- All workflow definitions pass DAG structure checks and dependency resolution validation without errors.
- Task dependency graphs resolve deterministically with valid upstream relationships declared via the SDK interface.
- Static verification checks confirm that workflow modules do not reference unapproved internal engine runtime structures.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline executing static verification and workflow parsing checks.
- Verified by: Peer review on pull requests modifying workflow definition files.
- Violation handling: Pull requests introducing direct imports of deprecated or internal engine modules without SDK usage will be blocked.
- Violation handling: Workflow definitions failing DAG parsing or dependency resolution checks will fail the automated build.
- Exception process: Submit an architectural review request with justification when a legacy component cannot use the standard SDK module.
- Exception process: Document any approved temporary exemption in the central architectural registry.
</enforcement>
