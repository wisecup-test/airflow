---
version: 2
name: cross-cutting-workflow-task-definitions-requiring-stan-7318
description: "Adoption of airflow.providers.standard.operators.bash for Workflow Task Execution: Workflow Task Definitions Requiring Standard Command"
metadata:
  actual:
    adr_id: 7318e006-ca63-4a18-babc-53f50e018fe5
    status: accepted
    candidate_group_id: 91eb57ca-62cd-42b7-8749-d1131cb8b266
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/dags/**
---

# Adoption of airflow.providers.standard.operators.bash for Workflow Task Execution: Workflow Task Definitions Requiring Standard Command

Scope: applies to `dev/airflow_perf/dags/**`.

These rules are ALWAYS ACTIVE for:
- Workflow DAG definitions defining executable script or command tasks.
- Performance benchmarking and workflow orchestration task graphs.

### Rules

- **R-LM-001** MUST: Workflow task definitions requiring standard command execution MUST instantiate tasks using airflow.providers.standard.operators.bash.

### Verify

- Discover and run the project test suite to verify workflow DAG integrity and task parsing.
- Discover and execute the static analysis and linting checks configured in the repository to ensure operator import compliance.

**Accept when:**

- All workflow DAG definitions successfully parse without import or configuration errors.
- Static analysis confirms task definitions adhere to standard provider operator patterns and relationship protocols.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks executing DAG parsing tests and static linting.
- Verified by: Peer code review evaluating task operator usage and dependency graph construction.
- Violation handling: Pull requests introducing non-standard command execution mechanisms or ungrounded versions will be blocked until corrected.
- Exception process: Submit an architectural exception request documenting why standard provider operators cannot fulfill task execution requirements.
</enforcement>
