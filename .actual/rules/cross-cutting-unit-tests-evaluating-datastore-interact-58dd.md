---
version: 2
name: cross-cutting-unit-tests-evaluating-datastore-interact-58dd
description: "github.com/apache/airflow/go-sdk/pkg/api Client Module: Unit Tests Evaluating Datastore Interactions Utilize"
metadata:
  actual:
    adr_id: 58dd9ac9-047d-4f7c-95c5-29e006638059
    status: accepted
    candidate_group_id: 1813deb9-82ef-488c-a06f-59ea8422b5cb
    evidence:
      source: deep
  scope:
    root: go-sdk
    paths:
      - edge/**
      - pkg/api/**
      - pkg/config/**
      - pkg/edgeapi/**
      - pkg/logging/server/**
      - sdk/**
---

# github.com/apache/airflow/go-sdk/pkg/api Client Module: Unit Tests Evaluating Datastore Interactions Utilize

Scope: applies to `go-sdk/edge/**`, `go-sdk/pkg/api/**`, `go-sdk/pkg/config/**`, `go-sdk/pkg/edgeapi/**`, `go-sdk/pkg/logging/server/**`, `go-sdk/sdk/**`.

These rules are ALWAYS ACTIVE for:
- Code executing within distributed worker runtimes, task execution handlers, and client packages requiring datastore operations.
- Components reading or modifying variables, connections, assets, task instances, or dag runs.

### Rules

- **R-PD-001** SHOULD: Unit tests evaluating datastore interactions SHOULD utilize corresponding mock client implementations rather than initiating external network connections.

### Verify

- Discover the project verification script from the repository manifest and execute test suites covering client interface implementations.
- Discover and run static analysis directives to confirm datastore interactions occur exclusively via client interfaces.

**Accept when:**

- All test suites for client interfaces and mock implementations execute successfully with zero failures.
- No direct database connection routines or raw unauthenticated requests are present within distributed worker packages.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline executing repository test and lint suites.
- Verified by: Mandatory peer review on pull requests touching datastore client packages.
- Violation handling: Pull requests bypassing client interface abstractions are blocked from merging.
- Violation handling: Non-compliant code must be refactored to use standard client interfaces prior to release.
- Exception process: Submit an architectural exception request detailing technical blockers preventing client usage.
- Exception process: Approval required from Platform Architecture Lead and Security Team.
</enforcement>
