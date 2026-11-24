---
version: 2
name: cross-cutting-inspect-repository-dependency-manifests-b391
description: "Adoption of pathlib for Object-Oriented Filesystem Path Operations: Inspect Repository Dependency Manifests Authoritative Lock"
metadata:
  actual:
    adr_id: b3914c14-268a-414d-b01f-63b20d86fd41
    status: accepted
    candidate_group_id: 856787ff-8f11-4941-bc74-ebebdb89c5af
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/docs/utils/**
      - src/sphinx_exts/**
      - src/tests_common/**
---

# Adoption of pathlib for Object-Oriented Filesystem Path Operations: Inspect Repository Dependency Manifests Authoritative Lock

Scope: applies to `devel-common/src/docs/utils/**`, `devel-common/src/sphinx_exts/**`, `devel-common/src/tests_common/**`.

These rules are ALWAYS ACTIVE for:
- Modules providing developer tooling, build orchestration, documentation generation, search indexing, and test utilities.
- Internal filesystem path construction, hierarchy navigation, directory search, and file content reading.

### Rules

- **R-LM-001** MUST: Inspect repository dependency manifests and authoritative lock artifacts to resolve exact runtime versions before adopting version-sensitive path interfaces.

### Verify

- Discover and run the project static analysis suite to verify that path manipulation complies with the object-oriented path interface standard.
- Discover and run the project type checking suite to ensure all path arguments adhere to Path type annotations.
- Discover and run the project test execution suite to validate that filesystem navigation and relative path resolutions succeed across target environments.

**Accept when:**

- All path manipulations across developer tooling and build modules use Path objects for navigation, resolution, and inspection.
- Interface contracts across internal modules annotate path parameters and return values with Path type structures.
- Automated static analysis and type verification directives pass without detecting procedural path concatenations in targeted modules.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis and linting checks executed during continuous integration workflows.
- Verified by: Static type verification suites checking path parameter annotations across internal modules.
- Verified by: Peer review validation on all code modifications touching developer and build tooling infrastructure.
- Violation handling: Continuous integration verification runs fail upon detecting procedural path manipulation routines in targeted modules.
- Violation handling: Pull requests containing unencapsulated string path manipulations are blocked until refactored to conform with object-oriented path standards.
- Exception process: Submit an architecture exception request detailing third-party boundary compatibility constraints that strictly mandate string primitives.
- Exception process: Document approved exceptions with explanatory inline comments and isolate primitive conversions at the outermost integration boundary.
</enforcement>
