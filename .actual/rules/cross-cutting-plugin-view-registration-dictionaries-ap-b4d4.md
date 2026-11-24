---
version: 2
name: cross-cutting-plugin-view-registration-dictionaries-ap-b4d4
description: "Flask-AppBuilder View Exposure via AirflowPlugin: Plugin View Registration Dictionaries Appbuilder Views"
metadata:
  actual:
    adr_id: b4d469b6-8149-4cab-8cff-c3514da384ee
    status: accepted
    candidate_group_id: 0d5f5795-258d-43b1-94ca-f508a61bab56
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - docs/empty_plugin/**
---

# Flask-AppBuilder View Exposure via AirflowPlugin: Plugin View Registration Dictionaries Appbuilder Views

Scope: applies to `airflow-core/docs/empty_plugin/**`.

These rules are ALWAYS ACTIVE for:
- Custom UI plugin implementations contributing HTTP endpoints or web views to the host application interface.

### Rules

- **R-SAB-001** SHOULD: Plugin view registration dictionaries in appbuilder_views SHOULD define a category and display name alongside the instantiated view to maintain consistent navigation hierarchy.

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
