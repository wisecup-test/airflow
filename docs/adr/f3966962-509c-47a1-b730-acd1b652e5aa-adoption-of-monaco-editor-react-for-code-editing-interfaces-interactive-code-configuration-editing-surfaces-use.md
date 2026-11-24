---
version: 2
name: f3966962-509c-47a1-b730-acd1b652e5aa-adoption-of-monaco-editor-react-for-code-editing-interfaces-interactive-code-configuration-editing-surfaces-use
description: "Adoption of @monaco-editor/react for Code Editing Interfaces: Interactive Code Configuration Editing Surfaces Use"
metadata:
  actual:
    adr_id: f3966962-509c-47a1-b730-acd1b652e5aa
    status: accepted
    candidate_group_id: e932463c-7aeb-4c27-aa9a-fca47649dfcc
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/ui/src/**
---

# Adoption of @monaco-editor/react for Code Editing Interfaces: Interactive Code Configuration Editing Surfaces Use

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/ui/src/**`

## Context

- The administrative user interface requires interactive code and configuration editing capabilities with syntax highlighting and language support.
- Integrating rich editor functionality within a declarative component architecture requires structured lifecycle management and worker isolation.
- Global editor environment settings must coordinate background worker scripts to avoid blocking the main browser thread.

## Problem Statement

Embedding complex code editing functionality directly into individual UI views without standardized wrappers leads to inconsistent configuration, unmanaged worker scripts blocking the main thread, and disjointed theming across application color modes.

## Decision

- **R-LM-001** MUST: Interactive code and configuration editing surfaces MUST use @monaco-editor/react as the standard component abstraction rather than direct DOM editor integrations.

## Policy Block

In scope:

- All user interface components presenting interactive code, configuration, or structured text editing surfaces.
- Application theme providers coordinating color tokens with editor syntax themes.

Out of scope:

- Plain text single-line form inputs and standard form text areas.
- Non-interactive read-only log viewers that use specialized streaming display components.

Exceptions:

- **EXC-001** Displaying static read-only code snippets that do not require editing, completion, or syntax diagnostics.
  Approval: Frontend architecture team approval
  Documentation: Document the rationale in the view component documentation and link to the approved architecture exception ticket.

## Rationale

- Adopting @monaco-editor/react provides declarative component integration while abstracting direct editor instance lifecycle manipulation.
- Configuring the global editor environment with dedicated worker URL resolvers prevents language parsing and tokenization from degrading UI responsiveness.
- Synchronizing color themes reactively ensures visual consistency across application dark and light modes.

## Consequences

Positive:

- Consistent syntax highlighting, code navigation, and editing capabilities across all administrative views.
- Separation of resource-heavy language parsing and worker threads from the main UI thread.
- Centralized theming architecture that aligns code presentation with application color modes.

Negative:

- Increased client bundle size and additional operational complexity for web worker asset packaging and hosting.
- Requirement to maintain global environment state and synchronization hooks across dynamic application theme transitions.

## Alternatives

- Direct integration with the core editor distribution without React wrapper abstractions (rejected)
  Rejected because: Increases lifecycle synchronization overhead and manual DOM reconciliation complexity within declarative UI trees.
  When valid: Valid in non-declarative UI environments or lightweight embedding contexts that do not use component-driven rendering.
- Plain textareas with external syntax highlighting overlays (rejected)
  Rejected because: Lacks rich editing capabilities, language server protocol features, and background worker tokenization.
  When valid: Valid when minimal resource consumption is prioritized over advanced editor capabilities.

## Risks

- Worker resolution failures in restrictive network environments or non-standard asset hosting setups.
  Mitigation: Configure fallback mechanisms and structured error logging when worker initialization fails.
  Owner: engineering team
- Client bundle bloat from heavyweight editor dependencies affecting initial page load metrics.
  Mitigation: Use dynamic component loading to defer editor asset evaluation until the editor view is accessed.
  Owner: engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Encapsulate worker creation within dedicated utility functions that handle URL resolution and runtime fallbacks.
- Ensure color mode change listeners propagate token theme definitions to active editor instances before mounting interactive views.

## Continuation Context

Verify commands:

- Discover and execute the project unit and integration test suite to validate editor component behavior.
- Discover and execute the project static analysis and linting checks to confirm import and configuration compliance.

Accept when:

- The component test suite verifies that editor instances render and receive correct language and theme configurations.
- Worker bootstrapping completes without uncaught global runtime exceptions during editor initialization.

## Enforcement

- Verified by: Automated static analysis checks detecting unauthorized third-party editor imports.
- Verified by: Code review verification of component wrappers and environment configuration lifecycles.
- Violation handling: Flag non-compliant code during automated linting and pull request reviews.
- Violation handling: Block pull request merges until unapproved editor components or direct global mutations are refactored into the centralized wrapper.
- Exception process: Submit an architectural change proposal detailing why an alternative text or code input mechanism is required for the specific view.
- Exception process: Obtain approval from frontend architectural leads prior to introducing unmanaged editing components.
