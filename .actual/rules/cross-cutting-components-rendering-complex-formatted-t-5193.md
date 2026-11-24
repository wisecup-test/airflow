---
version: 2
name: cross-cutting-components-rendering-complex-formatted-t-5193
description: "react-i18next Adoption for Frontend Internationalization: Components Rendering Complex Formatted Text Containing"
metadata:
  actual:
    adr_id: 5193208d-539a-4ba3-8298-572bbfad6522
    status: accepted
    candidate_group_id: 9e7e6e86-042a-44e8-a628-ef406a14245c
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/ui/src/components/**
---

# react-i18next Adoption for Frontend Internationalization: Components Rendering Complex Formatted Text Containing

Scope: applies to `airflow-core/src/airflow/ui/src/components/**`.

These rules are ALWAYS ACTIVE for:
- Frontend user interface components rendering user-facing text, labels, badges, dialogs, forms, or data displays.
- Presentation layer modules within the web interface requiring textual localization.

### Rules

- **R-LM-001** MAY: Components rendering complex formatted text containing markup or nested interactive elements MAY use the Trans component from react-i18next.

### Verify

- Discover and run the project's linter scripts to ensure no hardcoded user-facing strings are introduced in presentation components.
- Discover and run the project's test suite to verify that UI components render correctly with translated strings.
- Discover and execute the type check validation scripts to confirm translation hook typings and component props conform to repository standards.

**Accept when:**

- Verification test suites pass without translation key resolution errors.
- Static linting checks verify that user-facing text is accessed via the internationalization library.
- UI component rendering tests execute successfully across defined locales.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated continuous integration pipeline running test suites, linting, and type checking.
- Verified by: Peer code review verifying adherence to internationalization patterns in component pull requests.
- Violation handling: Continuous integration checks block merging of code with unresolved translation keys or hardcoded UI text.
- Violation handling: Code review feedback requires migrating raw string literals to the translation hook.
- Exception process: Exceptions for static non-translatable text must be documented in pull requests and approved by the frontend engineering lead.
</enforcement>
