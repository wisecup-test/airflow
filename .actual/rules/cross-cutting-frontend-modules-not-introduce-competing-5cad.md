---
version: 2
name: cross-cutting-frontend-modules-not-introduce-competing-5cad
description: "Adoption of @chakra-ui/react Component Library: Frontend Modules Not Introduce Competing Third"
metadata:
  actual:
    adr_id: 5cada80c-bd5b-4918-addd-a8acf4d31b99
    status: accepted
    candidate_group_id: a268c66b-0711-41d3-b4d1-505a32c6b152
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# Adoption of @chakra-ui/react Component Library: Frontend Modules Not Introduce Competing Third

Scope: applies to `airflow-core/src/airflow/**`.

These rules are ALWAYS ACTIVE for:
- All frontend user interface modules, components, forms, modals, and operational views within the project.

### Rules

- **R-LM-001** MUST_NOT: Frontend modules MUST NOT introduce competing third-party design system component libraries that duplicate the component primitives provided by @chakra-ui/react.

### Verify

- Discover and run the project static analysis and linting command declared in project configuration scripts.
- Discover and execute the frontend build command defined in the dependency manifest to verify component compilation.
- Discover and run the frontend automated test suite to ensure component rendering and integration integrity.

**Accept when:**

- All frontend components import UI primitives according to design system guidelines and pass static type verification.
- The frontend build completes successfully with zero compilation or style resolution errors.
- Frontend test suites execute cleanly with all component rendering and state interaction tests passing.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated linting rules that inspect module imports in user interface files.
- Verified by: Continuous integration checks that validate frontend build integrity and type safety.
- Verified by: Peer code reviews on pull requests affecting user interface presentation layers.
- Violation handling: Pull requests introducing unapproved parallel component libraries or unvetted UI frameworks MUST be blocked by automated checks.
- Violation handling: Violations identified in review must be refactored to consume approved component primitives before merge approval.
- Exception process: Submit an architectural exception request detailing technical constraints preventing the use of standard component primitives.
- Exception process: Obtain approval from frontend architecture maintainers prior to merging bespoke component primitives.
- Exception process: Document accepted exceptions with an agreed timeline for aligning with the shared component library.
</enforcement>
