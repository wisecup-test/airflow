---
version: 2
name: cross-cutting-frontend-presentation-components-import-4dfd
description: "Adoption of react-icons/fi for User Interface Iconography: Frontend Presentation Components Import Iconography Directly"
metadata:
  actual:
    adr_id: 4dfd565d-d0fc-4172-af08-06b154422d12
    status: accepted
    candidate_group_id: 15a2a578-19d9-42ce-a6d7-350b5449995e
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/ui/src/**
---

# Adoption of react-icons/fi for User Interface Iconography: Frontend Presentation Components Import Iconography Directly

Scope: applies to `airflow-core/src/airflow/ui/src/**`.

These rules are ALWAYS ACTIVE for:
- User interface layout components requiring visual symbols or navigation cues.
- Action buttons, dropdown indicators, and status badges within frontend pages.

### Rules

- **R-LM-001** MUST: Frontend presentation components MUST import iconography components directly from react-icons/fi submodules to maintain consistent visual language across interface elements.

### Verify

- Discover and run the repository linter to verify that iconography imports conform to authorized submodule paths.
- Discover and execute the frontend type-checking and build validation commands to confirm icon symbol resolutions.

**Accept when:**

- Repository static analysis passes with zero import violations regarding iconography module paths.
- Frontend compilation completes successfully with all icon symbols resolved from authorized submodules.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks in continuous integration pipelines.
- Verified by: Peer code review for visual consistency and proper subpath import structure.
- Violation handling: Pull requests containing disallowed root imports or unapproved inline SVG markup are blocked until corrected.
- Exception process: Exceptions for unique proprietary graphics or fallback icon subsets must be submitted via design review approval.
</enforcement>
