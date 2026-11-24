---
version: 2
name: ce21eeb0-1d47-401d-999d-c423eff539e3-standardization-on-airflow-exceptions-for-domain-error-and-lifecycle-signaling-subsystem-fixtures-testing-deprecated-configuration-interfaces
description: "Standardization on airflow.exceptions for Domain Error and Lifecycle Signaling: Subsystem Fixtures Testing Deprecated Configuration Interfaces"
metadata:
  actual:
    adr_id: ce21eeb0-1d47-401d-999d-c423eff539e3
    status: accepted
    candidate_group_id: 20fa7624-49de-4fba-9998-b7c59024f5cb
    evidence:
      source: deep
  scope:
    root: devel-common
    paths:
      - src/tests_common/test_utils/**
---

# Standardization on airflow.exceptions for Domain Error and Lifecycle Signaling: Subsystem Fixtures Testing Deprecated Configuration Interfaces

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `devel-common`

Applies to:
- `src/tests_common/test_utils/**`

## Context

- Execution wrappers, connection fixtures, and task watchers require standardized error handling that integrates with orchestration lifecycle management.
- Generic language exceptions fail to trigger domain-specific retry, deferral, and task failure transitions when propagated across execution boundaries.
- Deprecation cycles across provider packages require standardized warning classes to alert consumers without breaking runtime execution.
- Testing infrastructure and shared utility modules must mirror runtime failure semantics to provide fidelity during validation.

## Problem Statement

Shared execution helpers, test utilities, and integration fixtures across the repository require a unified exception and lifecycle signaling mechanism. Using heterogeneous standard library exceptions leads to unhandled failures, improper retry classification, and failure to propagate task states cleanly across orchestration boundaries. A centralized error and warning hierarchy is necessary to ensure consistent exception semantics, warning detection, and failure recovery across both runtime components and test harnesses.

## Decision

- **R-LM-001** SHOULD: Subsystem fixtures testing deprecated configuration interfaces or legacy parameters SHOULD catch and assert warnings using AirflowProviderDeprecationWarning rather than generic warning classes.

## Policy Block

In scope:

- Shared utility modules, test helpers, connector setup fixtures, and execution wrappers interacting with framework orchestration.
- Error reporting, command execution monitoring, and task state propagation boundaries.

Out of scope:

- Low-level serialization primitives and pure mathematical functions with no dependency on orchestration lifecycle.
- Third-party client libraries raising foreign transport-level exceptions before reaching helper translation layers.

Exceptions:

- **EXC-20-001** Interfacing directly with third-party protocol parsers where external exception types must be caught prior to domain translation.
  Approval: Architecture review team
  Documentation: Inline comments detailing the external exception boundary and translation logic.

## Rationale

- Evidence across seven shared utility and test modules demonstrates consistent adoption of airflow.exceptions across execution logging, deferrable operator harnesses, connection helpers, and watcher tasks.
- Raising AirflowException ensures operational failures trigger appropriate scheduler state machines and task failure handlers across execution environments.
- Standardizing on AirflowProviderDeprecationWarning enables systematic detection of deprecation cycles in shared messaging and provider test suites.
- Standardizing exceptions prevents divergence between production execution semantics and test utility behaviors.

## Consequences

Positive:

- Ensures predictable failure classification and state transitions across command execution, connection setup, and deferrable operator lifecycles.
- Enables task watchers and orchestrator schedulers to reliably detect operational failures and trigger expected retry behavior.
- Standardizes deprecation warning verification across integration test suites without interrupting active pipeline execution.

Negative:

- Couples shared utility and test modules directly to the framework core exception package.
- Requires wrapping lower-level operating system and protocol exceptions into domain-specific exception instances.

## Alternatives

- Standard library runtime exceptions for utility and task failure signaling (rejected)
  Rejected because: Generic exceptions lack framework lifecycle semantics and cause orchestrator schedulers to misclassify operational failures as unexpected runtime faults.
  When valid: In isolated standalone utility libraries with zero framework dependencies.
- Decoupled custom exception hierarchies per utility module (rejected)
  Rejected because: Fragmented exception trees require disparate error handling across callers and prevent uniform failure detection by the framework engine.
  When valid: When building multi-framework abstraction adapters requiring zero engine-specific imports.

## Risks

- Overuse of base framework exceptions may obscure underlying root causes if original tracebacks are suppressed.
  Mitigation: Include root cause exceptions and detailed standard error messages in exception arguments to preserve diagnostic context.
  Owner: Engineering team
- Breaking changes in the framework exception hierarchy across major upstream updates.
  Mitigation: Enforce lock-file version grounding and verify exception signatures against locked version documentation.
  Owner: Engineering team

## Implementation Notes

- DISCOVERY POLICY (MANDATORY): This ADR omits all tool names, file names, commands, package managers, and version numbers. The consumer MUST derive them from the project repository.

LOCK-VERSION GROUNDING (MANDATORY) — before writing code that uses a versioned library, execute in order:
1. Find the dependency manifest in the repo. It declares ranges, not installed versions.
2. Identify the build tool from the manifest.
3. Inspect the repository lock or resolution artifact to determine the exact resolved version. This artifact is authoritative; build-tool output only verifies the active environment matches it.
4. Look up the official documentation, changelog, or public API reference for that exact version. Do not use training-data recall — fetch or search the public internet for version-specific docs.
5. Confirm every API, class, or function you will call exists in that exact version's documentation before using it.
6. For version-sensitive behavior, re-run steps 3-5 per dependency at point of use.
- When capturing command execution failures, include both standard output and standard error content in the raised exception message.
- Verify credential file paths and structure prior to parsing, raising framework exceptions on invalid formats.

## Continuation Context

Verify commands:

- find . -maxdepth 3 -type f -name "*test*" -exec echo "Running verification suite: {}" \;
- find . -maxdepth 3 -type f -name "*lint*" -exec echo "Running compliance analysis: {}" \;

Accept when:

- All command execution, connection helper, and task watcher modules raise AirflowException on operational failure conditions.
- Automated test suites pass with zero unhandled standard library runtime exceptions in framework utility boundaries.

## Enforcement

- Verified by: Automated continuous integration test suites and static analysis gates.
- Verified by: Peer review during pull request evaluation.
- Violation handling: Pull requests introducing raw unhandled system exceptions in framework utility boundaries are rejected.
- Violation handling: Static analysis violations block deployment until compliant exception classes are utilized.
- Exception process: Submit an architectural review request explaining why the framework exception hierarchy cannot be imported or raised.
- Exception process: Document the approved non-standard exception boundary and rationale in module documentation.
