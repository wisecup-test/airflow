---
version: 2
name: cross-cutting-before-implementing-components-that-depe-21dc
description: "Flask-AppBuilder View Exposure via AirflowPlugin: Before Implementing Components That Depend Web"
metadata:
  actual:
    adr_id: 21dc39ba-3212-45a8-9d18-b5f646dff21f
    status: accepted
    candidate_group_id: 0d5f5795-258d-43b1-94ca-f508a61bab56
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - docs/empty_plugin/**
---

# Flask-AppBuilder View Exposure via AirflowPlugin: Before Implementing Components That Depend Web

Scope: applies to `airflow-core/docs/empty_plugin/**`.

These rules are ALWAYS ACTIVE for:
- Custom UI plugin implementations contributing HTTP endpoints or web views to the host application interface.

### Rules

- **R-SAB-001** MUST: Before implementing components that depend on web view or plugin libraries, developers MUST resolve and inspect the project dependency lock artifact to determine the exact installed library versions.

### Verify

- Discover the repository test runner from project configuration and execute test suites targeting plugin registration and view endpoints.
- Discover the static analysis tool configured in the repository and run boundary verification rules checking plugin view inheritance and permission decorators.

**Accept when:**

- All plugin view endpoints register under the application view hierarchy with role-based access decorators applied.
- Automated verification confirms no unsecured or unannotated public view methods exist within custom plugin classes.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checking for required authorization decorators on all exposed view methods.
- Verified by: Automated integration tests validating plugin view registration and endpoint access boundaries.
- Violation handling: Pull requests introducing view methods without proper access decorators or non-standard plugin registration are blocked.
- Exception process: Submit an architectural review request to the security and architecture teams for headless or non-standard view routing.
</enforcement>
