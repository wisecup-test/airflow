---
version: 2
name: cross-cutting-baseview-subclasses-declare-default-view-afe8
description: "Flask-AppBuilder View Exposure via AirflowPlugin: Baseview Subclasses Declare Default View Attribute"
metadata:
  actual:
    adr_id: afe862c0-36d2-4f27-a38d-13cd5725b77e
    status: accepted
    candidate_group_id: 0d5f5795-258d-43b1-94ca-f508a61bab56
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - docs/empty_plugin/**
---

# Flask-AppBuilder View Exposure via AirflowPlugin: Baseview Subclasses Declare Default View Attribute

Scope: applies to `airflow-core/docs/empty_plugin/**`.

These rules are ALWAYS ACTIVE for:
- Custom UI plugin implementations contributing HTTP endpoints or web views to the host application interface.

### Rules

- **R-SAB-001** MUST: BaseView subclasses MUST declare a default_view attribute pointing to a valid exposed endpoint method.

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
