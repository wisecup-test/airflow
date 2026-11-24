---
version: 2
name: cross-cutting-view-transitions-link-rendering-execute-4bff
description: "Adoption of react-router-dom for Client-Side Routing and Navigation: View Transitions Link Rendering Execute Through"
metadata:
  actual:
    adr_id: 4bff2f9c-e769-4c50-961d-2a7e55805db2
    status: accepted
    candidate_group_id: 17b94952-60da-4ce9-a8e3-a0adda2dd2ef
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of react-router-dom for Client-Side Routing and Navigation: View Transitions Link Rendering Execute Through

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- User interface applications requiring client-side routing, view switching, and navigation.
- Components, custom hooks, and layout hierarchies that interact with route paths and search parameters.

### Rules

- **R-LM-001** MUST: View transitions and link rendering MUST execute through react-router-dom routing components and navigation primitives rather than native window location mutations.

### Verify

- Discover and run the project static analysis and linting suite to ensure all client navigation imports reference react-router-dom.
- Discover and execute frontend automated unit and component tests to confirm route matching and navigation behaviors pass.

**Accept when:**

- Static analysis validates all client routing and search parameter usages originate from react-router-dom without direct window location mutations.
- All frontend unit and integration test suites pass with full router context resolution.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration checks executing lint rules and type validation on pull requests.
- Verified by: Peer code review verifying adherence to centralized navigation hooks and search parameter patterns.
- Violation handling: Pull requests containing unapproved routing libraries or raw location manipulation are blocked until corrected.
- Exception process: Architectural review and exception approval documented in writing before introducing alternative routing mechanisms.
</enforcement>
