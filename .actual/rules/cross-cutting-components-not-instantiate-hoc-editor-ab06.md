---
version: 2
name: cross-cutting-components-not-instantiate-hoc-editor-ab06
description: "Adoption of @monaco-editor/react for Code Editing Interfaces: Components Not Instantiate Hoc Editor Instances"
metadata:
  actual:
    adr_id: ab062cce-b47c-4da4-82b2-7d4674aeebab
    status: accepted
    candidate_group_id: e932463c-7aeb-4c27-aa9a-fca47649dfcc
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/ui/src/**
---

# Adoption of @monaco-editor/react for Code Editing Interfaces: Components Not Instantiate Hoc Editor Instances

Scope: applies to `airflow-core/src/airflow/ui/src/**`.

These rules are ALWAYS ACTIVE for:
- All user interface components presenting interactive code, configuration, or structured text editing surfaces.
- Application theme providers coordinating color tokens with editor syntax themes.

### Rules

- **R-LM-001** MUST_NOT: Components MUST NOT instantiate ad-hoc editor instances or bypass the centralized editor wrapper component.

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
