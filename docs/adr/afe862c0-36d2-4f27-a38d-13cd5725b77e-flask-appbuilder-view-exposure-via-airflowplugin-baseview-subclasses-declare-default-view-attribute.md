---
version: 2
name: afe862c0-36d2-4f27-a38d-13cd5725b77e-flask-appbuilder-view-exposure-via-airflowplugin-baseview-subclasses-declare-default-view-attribute
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

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `docs/empty_plugin/**`

## Context

- Web interface extensions require standardized boundary contracts to integrate with host application routing and security.
- Custom plugins need a predictable interface to contribute views without modifying core web application routing code.
- The codebase demonstrates plugin view integration using Flask-AppBuilder BaseView classes registered through AirflowPlugin appbuilder_views structures.

## Problem Statement

Custom web user interface extensions require standardized integration contracts to mount endpoints into the application web interface while enforcing role-based access control. In the absence of an enforced service boundary pattern, custom views risk exposing unsecured routes, bypassing navigation hierarchies, and creating architectural fragmentation.

## Decision

- **R-SAB-001** MUST: BaseView subclasses MUST declare a default_view attribute pointing to a valid exposed endpoint method.

## Policy Block

In scope:

- Custom UI plugin implementations contributing HTTP endpoints or web views to the host application interface.

Out of scope:

- Core web application views defined directly within the primary application package.
- Non-UI plugins such as operators, sensors, hooks, or headless background task extensions.

## Rationale

- Subclassing Flask-AppBuilder BaseView within AirflowPlugin establishes a uniform service definition boundary for web extensions.
- Mandating access control decorators on exposed view methods guarantees consistent role-based access enforcement across external plugins.
- Registering views through standardized plugin configuration maps enables the core application to catalog and render navigation elements dynamically.

## Consequences

Positive:

- Standardizes service boundary definitions and discovery for external web UI plugins.
- Ensures uniform enforcement of role-based access control across all custom view endpoints.
- Integrates custom endpoints into the core application navigation hierarchy automatically.

Negative:

- Tightly couples custom UI view definitions to the Flask-AppBuilder framework architecture.
- Requires additional boilerplate code for registering even trivial presentation views.

## Alternatives

- Direct Blueprint routing without Flask-AppBuilder BaseView (rejected)
  Rejected because: Bypasses integrated role-based access control and navigation menu registration provided by the web framework.
  When valid: Valid when exposing standalone headless endpoints that manage custom token authentication independently.
- External standalone web service integration (rejected)
  Rejected because: Introduces substantial operational complexity and network latency for lightweight UI extensions.
  When valid: Valid when extension features require dedicated compute scaling or incompatible runtime isolation.

## Risks

- Tight coupling to Flask-AppBuilder view classes complicates potential future web framework migrations.
  Mitigation: Isolate presentation rendering within thin controller views and delegate business logic to decoupled service layers.
  Owner: Engineering Team
- Omitting authorization decorators on exposed endpoints could permit unauthenticated access to plugin views.
  Mitigation: Enforce static analysis checks requiring authorization decorators on every exposed view endpoint.
  Owner: Security Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Ensure template paths rendered by BaseView endpoints use namespaced directories to prevent template naming collisions.
- Populate category and display name keys in the appbuilder_views dictionary to enable correct navigation menu placement.

## Continuation Context

Verify commands:

- Discover the repository test runner from project configuration and execute test suites targeting plugin registration and view endpoints.
- Discover the static analysis tool configured in the repository and run boundary verification rules checking plugin view inheritance and permission decorators.

Accept when:

- All plugin view endpoints register under the application view hierarchy with role-based access decorators applied.
- Automated verification confirms no unsecured or unannotated public view methods exist within custom plugin classes.

## Enforcement

- Verified by: Automated static analysis checking for required authorization decorators on all exposed view methods.
- Verified by: Automated integration tests validating plugin view registration and endpoint access boundaries.
- Violation handling: Pull requests introducing view methods without proper access decorators or non-standard plugin registration are blocked.
- Exception process: Submit an architectural review request to the security and architecture teams for headless or non-standard view routing.
