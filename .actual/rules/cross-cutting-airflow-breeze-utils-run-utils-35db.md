---
version: 2
name: cross-cutting-airflow-breeze-utils-run-utils-35db
description: "Internal Process Execution Module Adoption: airflow_breeze.utils.run_utils: Command Execution Failures Capture Standard Output"
metadata:
  actual:
    adr_id: 35dbb057-4456-46d6-8a2c-f9138e507eb1
    status: accepted
    candidate_group_id: fd44c577-7cd4-4bd9-a179-0cee354d527b
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/utils/**
---

# Internal Process Execution Module Adoption: airflow_breeze.utils.run_utils: Command Execution Failures Capture Standard Output

Scope: applies to `dev/breeze/src/airflow_breeze/utils/**`.

These rules are ALWAYS ACTIVE for:
- Utility modules within developer tooling that require subprocess execution or host architecture inspection

### Rules

- **R-LM-001** MUST: Command execution failures MUST capture standard output and standard error streams and route formatted error notices to designated console presentation interfaces

### Verify

- grep -rn "airflow_breeze.utils.run_utils" .
- find . -type f -exec grep -H "airflow_breeze.utils.run_utils" {} +

**Accept when:**

- All utility modules dispatching external process executions import and use airflow_breeze.utils.run_utils
- No direct unmanaged process execution invocations exist in consumer utility modules
- The project test suite passes when verifying system command and environment utility behaviors

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks inspecting imports across utility modules
- Verified by: Peer code review validating adherence to shared utility module boundaries
- Violation handling: Code review rejection for any new utility module introducing unmanaged process execution calls
- Violation handling: Mandatory refactoring of non-compliant execution logic to route through airflow_breeze.utils.run_utils
- Exception process: Submit an architectural review request detailing why airflow_breeze.utils.run_utils cannot support the specific process execution requirements
- Exception process: Require approval from the maintainers before introducing alternative execution logic
</enforcement>
