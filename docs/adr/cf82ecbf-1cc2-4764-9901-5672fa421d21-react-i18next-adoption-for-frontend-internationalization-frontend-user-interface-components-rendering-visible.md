---
version: 2
name: cf82ecbf-1cc2-4764-9901-5672fa421d21-react-i18next-adoption-for-frontend-internationalization-frontend-user-interface-components-rendering-visible
description: "react-i18next Adoption for Frontend Internationalization: Frontend User Interface Components Rendering Visible"
metadata:
  actual:
    adr_id: cf82ecbf-1cc2-4764-9901-5672fa421d21
    status: accepted
    candidate_group_id: 9e7e6e86-042a-44e8-a628-ef406a14245c
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/ui/src/components/**
---

# react-i18next Adoption for Frontend Internationalization: Frontend User Interface Components Rendering Visible

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/ui/src/components/**`

## Context

- The frontend application encompasses numerous user-facing components spanning navigation, search, workflows, modals, forms, and data visualization.
- Displaying direct text literals across disparate component trees leads to inconsistent user messaging and prevents multi-language localization.
- Standardizing on react-i18next provides a unified mechanism across the UI component architecture to decouple display strings from component rendering logic.
- Analysis across UI component modules reveals consistent integration of react-i18next across modal dialogues, form inputs, status badges, and table views.

## Problem Statement

Hardcoding user-visible text directly inside frontend components tightly couples presentation markup with specific languages, impedes internationalization, and causes fragmented terminology across complex interface surfaces. The user interface requires a consistent, decoupled architecture for string localization and internationalization across all interactive elements, dialogs, and data displays.

## Decision

- **R-LM-001** MUST: Frontend user interface components rendering user-visible strings MUST use react-i18next to manage and resolve localized copy.

## Policy Block

In scope:

- Frontend user interface components rendering user-facing text, labels, badges, dialogs, forms, or data displays.
- Presentation layer modules within the web interface requiring textual localization.

Out of scope:

- Backend services, data processing pipelines, and internal server-side logging where user localization is not applicable.
- Non-visual utility functions, internal schema definitions, and machine-to-machine protocol payloads.

## Rationale

- Standardizing internationalization through react-i18next guarantees that all presentation components resolve UI copy through a shared translation interface.
- Decoupling localized content from component structure facilitates translation extraction, maintenance, and future locale support without modifying UI presentation logic.
- Broad adoption across dozens of UI components establishes a predictable pattern for frontend development and automated translation auditing.

## Consequences

Positive:

- Consistent internationalization handling across all frontend UI components and modals.
- Centralized translation management enabling localized copy updates without altering component layout logic.
- Separation of concerns between UI component presentation and language-specific text assets.

Negative:

- Additional boilerplate required to wrap user-facing text in translation hooks or components.
- Overhead of maintaining translation resource keys and keeping localized dictionaries synchronized.
- Potential runtime lookup cost for resolving translation strings during component rendering.

## Alternatives

- Hardcoded English string literals directly embedded in presentation components (rejected)
  Rejected because: Prevents multi-language localization, scatters user-facing strings across component trees, and creates inconsistent user terminology.
  When valid: Rapid prototyping or internal proof-of-concept components that will never be exposed to multilingual end users.
- Custom proprietary localization wrapper around browser locale APIs (rejected)
  Rejected because: Imposes maintenance overhead for custom pluralization, interpolation, and namespace management that standard ecosystem libraries already solve.
  When valid: Environments with severe bundle size restrictions where external dependencies cannot be accommodated.

## Risks

- Missing or misspelled translation keys causing unrendered or fallback text in production UI.
  Mitigation: Implement static analysis and build verification checks that validate translation keys against defined translation catalogs.
  Owner: Frontend engineering team
- Performance degradation from excessive re-renders caused by frequent locale or namespace state transitions.
  Mitigation: Scope translation namespaces to specific feature boundaries and utilize memoized translation selectors.
  Owner: Frontend engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- When constructing UI components, invoke the useTranslation hook at the component root and bind dynamic variables via interpolation parameters.
- Organize translation keys hierarchically by feature domain to prevent naming collisions and facilitate incremental namespace loading.
- For rich text containing inline links, emphasized spans, or formatting elements, use the Trans component rather than manual string concatenation.

## Continuation Context

Verify commands:

- Discover and run the project's linter scripts to ensure no hardcoded user-facing strings are introduced in presentation components.
- Discover and run the project's test suite to verify that UI components render correctly with translated strings.
- Discover and execute the type check validation scripts to confirm translation hook typings and component props conform to repository standards.

Accept when:

- Verification test suites pass without translation key resolution errors.
- Static linting checks verify that user-facing text is accessed via the internationalization library.
- UI component rendering tests execute successfully across defined locales.

## Enforcement

- Verified by: Automated continuous integration pipeline running test suites, linting, and type checking.
- Verified by: Peer code review verifying adherence to internationalization patterns in component pull requests.
- Violation handling: Continuous integration checks block merging of code with unresolved translation keys or hardcoded UI text.
- Violation handling: Code review feedback requires migrating raw string literals to the translation hook.
- Exception process: Exceptions for static non-translatable text must be documented in pull requests and approved by the frontend engineering lead.
