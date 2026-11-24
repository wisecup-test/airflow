---
version: 2
name: f24cf1ff-00c3-479c-8634-161e53745eb3-copy-module-for-object-duplication-and-state-isolation-consumers-inspect-repository-lock-artifact-verify
description: "copy Module for Object Duplication and State Isolation: Consumers Inspect Repository Lock Artifact Verify"
metadata:
  actual:
    adr_id: f24cf1ff-00c3-479c-8634-161e53745eb3
    status: accepted
    candidate_group_id: 1f54dd98-8d8d-4a73-bd07-545b1bdb268f
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/sphinx_exts/**
      - src/tests_common/test_utils/**
---

# copy Module for Object Duplication and State Isolation: Consumers Inspect Repository Lock Artifact Verify

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/sphinx_exts/**`
- `src/tests_common/test_utils/**`

## Context

- Components and test helpers manipulate mutable operational instances and dictionary mappings that require isolation across execution contexts.
- Internal test harnesses clone task attributes using copy.copy to prevent state mutations from leaking across test cases.
- Documentation reference resolvers and operator runners duplicate data structures to maintain independent reference lifecycles.

## Problem Statement

Shared mutable state across execution contexts, test helpers, and reference mappings introduces mutation side effects and race conditions, requiring an explicit mechanism to shallow clone objects and internal dictionaries.

## Decision

- **R-LM-001** MUST: Consumers MUST inspect the repository lock artifact and verify resolved runtime dependency declarations prior to introducing external cloning utilities.

## Policy Block

In scope:

- Test execution utilities, runtime helpers, and documentation cross-reference resolvers requiring object duplication.

Out of scope:

- Immutable data structures, primitive values, and stateless functional transforms.

## Rationale

- Using copy.copy provides an explicit mechanism for duplicating mutable mappings without introducing external third-party dependencies.
- Cloning task instance attributes and operational structures isolates test harnesses from unexpected upstream mutations.
- Shallow copying introduces minimal allocation overhead compared to full deep duplication while satisfying state isolation requirements.

## Consequences

Positive:

- Prevents unintended cross-test pollution and state leakage across operational contexts.
- Eliminates unexpected side effects when mutating internal dictionary representations.
- Relies on standard object duplication mechanisms without adding external runtime dependencies.

Negative:

- Shallow copies do not duplicate nested mutable objects, which may lead to shared references if nested objects are mutated.
- Explicit copy calls introduce minor memory allocation overhead across high-frequency execution paths.

## Alternatives

- Implicit in-place state mutation without copying (rejected)
  Rejected because: In-place mutation causes test pollution, race conditions, and unpredictable side effects across shared runtime references.
  When valid: Valid only for strictly private, single-use objects that are immediately discarded after execution.
- Mandatory recursive deep copying for all state objects (rejected)
  Rejected because: Deep copying introduces excessive serialization and memory overhead and can fail on complex cyclical references.
  When valid: Valid only when nested data structures contain deeply nested mutable collections that must be fully isolated.

## Risks

- Accidental shared mutation of nested mutable objects due to shallow copying behavior.
  Mitigation: Validate object graph depth and selectively apply deep copying when nested mutable containers are modified.
  Owner: Engineering Team
- Memory overhead from creating multiple shallow copies of large state dictionaries in tight loops.
  Mitigation: Scope object cloning strictly to context boundary crossings and lifecycle initialization points.
  Owner: Engineering Team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- Ensure that copy.copy is applied at instance initialization or prior to invoking mutating methods on shared references.
- Evaluate object reference graphs to verify whether shallow duplication is sufficient before relying on nested field modifications.

## Continuation Context

Verify commands:

- "${PROJECT_TEST_RUNNER:-true}"
- "${VERIFY_SUITE_COMMAND:-true}"

Accept when:

- All discovered test suites execute successfully without state contamination across test cases.
- Cloned instances retain independent attribute mappings when underlying dictionaries are modified.

## Enforcement

- Verified by: Automated continuous integration test suites.
- Verified by: Peer code review focusing on state duplication and mutation boundaries.
- Violation handling: Pull requests mutating shared runtime or test structures without explicit duplication are blocked during code review.
- Violation handling: State contamination failures in test suites trigger immediate review of object duplication logic.
- Exception process: Exceptions require approval from principal engineering leads with documented rationale demonstrating that the mutated object is strictly single-use.
