---
version: 2
name: cross-cutting-every-endpoint-exposed-baseview-subclass-8931
description: "Flask-AppBuilder View Exposure via AirflowPlugin: Every Endpoint Exposed Baseview Subclass Annotated"
metadata:
  actual:
    adr_id: 8931f1ff-7969-4f93-96e1-a1ca3aa81d43
    status: accepted
    candidate_group_id: 0d5f5795-258d-43b1-94ca-f508a61bab56
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - docs/empty_plugin/**
---

# Flask-AppBuilder View Exposure via AirflowPlugin: Every Endpoint Exposed Baseview Subclass Annotated

Scope: applies to `airflow-core/docs/empty_plugin/**`.

These rules are ALWAYS ACTIVE for:
- Custom UI plugin implementations contributing HTTP endpoints or web views to the host application interface.

### Rules

- **R-SAB-001** MUST: Every endpoint exposed on a BaseView subclass MUST be annotated with the expose decorator and an access view authorization decorator defining boundary permissions.

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
