---
version: 2
name: eec944bb-888e-49fa-b80a-adb84f69dd9d-adoption-of-diagrams-library-for-declarative-architecture-visualizations-consumers-inspect-repository-lock-artifact-determine
description: "Adoption of diagrams Library for Declarative Architecture Visualizations: Consumers Inspect Repository Lock Artifact Determine"
metadata:
  actual:
    adr_id: eec944bb-888e-49fa-b80a-adb84f69dd9d
    status: accepted
    candidate_group_id: 985b21dc-cbfe-4ee0-9590-b9e0d7b9dd2c
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - docs/**
      - hatch_build.py
---

# Adoption of diagrams Library for Declarative Architecture Visualizations: Consumers Inspect Repository Lock Artifact Determine

Date: 2025-05-15
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `docs/**`
- `hatch_build.py`

## Context

- Architectural documentation requires visual diagrams illustrating distributed system topologies, multi-team boundaries, and execution lifecycles.
- Binary image files maintained manually diverge from codebase changes and do not allow standard revision history tracking.
- Multiple documentation modules adopt declarative, code-defined graph abstractions to render architecture diagrams programmatically.

## Problem Statement

Manual diagram maintenance causes visual architectural drift, opaque image revisions, and inconsistent component semantics across documentation. The project requires a maintainable, code-driven mechanism to construct and update architecture visualizations alongside codebase changes.

## Decision

- **R-PEA-001** MUST: Consumers MUST inspect the repository lock artifact to determine and resolve the exact locked version of the diagrams library before implementing or updating diagram scripts.

## Policy Block

In scope:

- All system architecture overview diagrams, subsystem interaction diagrams, and lifecycle topology documentation assets.

Out of scope:

- Inline textual code comments, tabular interface specifications, and static user interface screenshots.

## Rationale

- Programmatic diagram definition integrates visual system topology directly into repository revision history.
- Adopting the diagrams library establishes reproducible, deterministic diagram rendering across build environments.
- Standardizing on programmatic diagrams minimizes synchronization latency between architectural evolution and documentation visuals.

## Consequences

Positive:

- Architecture visual assets are version-controlled and can be reviewed via standard change request workflows.
- Diagram visual semantics and component representations remain standardized across all documented subsystem topologies.
- Diagram artifact generation can be automated within continuous integration documentation workflows.

Negative:

- Diagram compilation introduces dependencies on graph layout rendering systems in the documentation build environment.
- Visual styling and spatial layout choices are constrained by the programmatic primitives provided by the diagramming framework.

## Alternatives

- Manual diagram authoring using external graphical editors (rejected)
  Rejected because: Lacks automated version control traceability and rapidly diverges from the underlying codebase.
  When valid: Informal whiteboard discussions and conceptual design ideation.
- Plain text structural descriptions without visual topology diagrams (rejected)
  Rejected because: Fails to clearly communicate complex multi-node interactions and distributed execution flows to consumers.
  When valid: Trivial single-component utilities with minimal cross-service interactions.

## Risks

- Missing graph layout system dependencies in local or automated environments causes diagram compilation failures.
  Mitigation: Document underlying graph rendering prerequisites and provide containerized or pre-configured build steps in continuous integration.
  Owner: engineering team
- Visual crowding or overlapping connection nodes as architectural complexity expands.
  Mitigation: Decompose complex distributed systems into modular subsystem diagrams rather than maintaining an overly dense single graphic.
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
- Locate diagram definition scripts alongside related documentation assets, ensuring each script exports a dedicated generation function.
- Group related architecture components using cluster scopes to reflect runtime isolation, identity domains, and scheduling boundaries.

## Continuation Context

Verify commands:

- Discover the documentation build invocation in the repository manifest and execute it to confirm diagram generation scripts run successfully.
- Inspect the project task runner configuration to locate and execute the diagram artifact verification routines.

Accept when:

- All architecture diagram generation scripts execute without error and produce expected visual output artifacts.
- Generated architecture diagrams reflect current subsystem boundaries and component topologies without manual intervention.

## Enforcement

- Verified by: Continuous integration documentation build workflows validating diagram compilation.
- Verified by: Peer code review on pull requests modifying architectural documentation assets.
- Violation handling: Pull requests adding un-sourced binary image diagrams or failing diagram scripts are blocked until programmatic definitions are supplied and pass verification.
- Exception process: Exceptions for proprietary visual assets or third-party logos require architectural review approval and documentation in the documentation metadata registry.
