---
version: 2
name: cross-cutting-web-worker-resolution-defined-through-04f2
description: "Adoption of @monaco-editor/react for Code Editing Interfaces: Web Worker Resolution Defined Through Global"
metadata:
  actual:
    adr_id: 04f24625-4973-4a25-813d-dd4fda024dd0
    status: accepted
    candidate_group_id: e932463c-7aeb-4c27-aa9a-fca47649dfcc
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/ui/src/**
---

# Adoption of @monaco-editor/react for Code Editing Interfaces: Web Worker Resolution Defined Through Global

Scope: applies to `airflow-core/src/airflow/ui/src/**`.

These rules are ALWAYS ACTIVE for:
- All user interface components presenting interactive code, configuration, or structured text editing surfaces.
- Application theme providers coordinating color tokens with editor syntax themes.

### Rules

- **R-LM-001** MUST: Web worker resolution MUST be defined through the global MonacoEnvironment configuration prior to mounting editor instances.

### Verify

- Discover and execute the project unit and integration test suite to validate editor component behavior.
- Discover and execute the project static analysis and linting checks to confirm import and configuration compliance.

**Accept when:**

- The component test suite verifies that editor instances render and receive correct language and theme configurations.
- Worker bootstrapping completes without uncaught global runtime exceptions during editor initialization.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks detecting unauthorized third-party editor imports.
- Verified by: Code review verification of component wrappers and environment configuration lifecycles.
- Violation handling: Flag non-compliant code during automated linting and pull request reviews.
- Violation handling: Block pull request merges until unapproved editor components or direct global mutations are refactored into the centralized wrapper.
- Exception process: Submit an architectural change proposal detailing why an alternative text or code input mechanism is required for the specific view.
- Exception process: Obtain approval from frontend architectural leads prior to introducing unmanaged editing components.
</enforcement>
