---
version: 2
name: cross-cutting-user-interface-components-client-renderi-0c5b
description: "Adoption of React as Core UI Component Library: User Interface Components Client Rendering Entrypoints"
metadata:
  actual:
    adr_id: 0c5b461a-ab28-481e-aca6-84d88116e43e
    status: accepted
    candidate_group_id: b3d915c9-1a3c-4fd4-87f0-bbf1746e2dbb
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - react-plugin-tools/react_plugin_template/src/**
---

# Adoption of React as Core UI Component Library: User Interface Components Client Rendering Entrypoints

Scope: applies to `dev/react-plugin-tools/react_plugin_template/src/**`.

These rules are ALWAYS ACTIVE for:
- Client-side user interface components, pages, and entrypoints within the plugin template.

### Rules

- **R-LM-001** MUST: User interface components and client rendering entrypoints MUST use React as their foundational UI component model and declarative rendering library.

### Verify

- Discover the repository test and type-checking scripts declared in the project configuration and execute them to verify that all React components compile and satisfy type constraints.
- Discover and execute the project build script to validate that component bundles render and package without errors.

**Accept when:**

- All user interface modules and client entrypoints compile without unresolved references or type errors.
- Client entrypoints mount root React component hierarchies successfully during project test and build execution.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated type checking and linting rules enforced during continuous integration.
- Verified by: Peer code reviews of component structure and entrypoint mounting logic.
- Violation handling: Pull requests introducing unapproved rendering libraries or direct imperative DOM mutations will be blocked at continuous integration and code review.
- Exception process: Exceptions must be documented in an architectural review and approved by technical leadership prior to introducing alternative component rendering libraries.
</enforcement>
