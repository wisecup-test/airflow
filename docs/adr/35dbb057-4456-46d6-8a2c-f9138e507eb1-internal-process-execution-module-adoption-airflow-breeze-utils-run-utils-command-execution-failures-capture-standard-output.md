---
version: 2
name: 35dbb057-4456-46d6-8a2c-f9138e507eb1-internal-process-execution-module-adoption-airflow-breeze-utils-run-utils-command-execution-failures-capture-standard-output
description: "Internal Process Execution Module Adoption: airflow_breeze.utils.run_utils: Command Execution Failures Capture Standard Output"
metadata:
  actual:
    adr_id: 35dbb057-4456-46d6-8a2c-f9138e507eb1
    status: accepted
    candidate_group_id: fd44c577-7cd4-4bd9-a179-0cee354d527b
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/utils/**
---

# Internal Process Execution Module Adoption: airflow_breeze.utils.run_utils: Command Execution Failures Capture Standard Output

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `breeze/src/airflow_breeze/utils/**`

## Context

- Developer tooling utilities require consistent mechanisms to execute shell commands, manage virtual environments, and inspect host platform capabilities
- Uncoordinated subprocess executions risk inconsistent error capture, unhandled return codes, and divergent console output formatting
- Static analysis observes multiple utility modules importing and delegating process execution to airflow_breeze.utils.run_utils

## Problem Statement

Developer tooling utility modules need to perform system interactions, virtual environment creation, and host architecture queries without duplicating process execution logic or fragmenting error handling conventions.

## Decision

- **R-LM-001** MUST: Command execution failures MUST capture standard output and standard error streams and route formatted error notices to designated console presentation interfaces

## Policy Block

In scope:

- Utility modules within developer tooling that require subprocess execution or host architecture inspection

Out of scope:

- Pure domain transformation modules that do not interact with the host operating system or external processes

## Rationale

- Adopting airflow_breeze.utils.run_utils establishes a uniform architectural boundary between utility business logic and low-level system process execution
- Centralizing process invocation enables consistent error parsing, standard exit code management, and coordinated user feedback across tools
- Evidence confirms consistent adoption of airflow_breeze.utils.run_utils across distinct utility components in the codebase

## Consequences

Positive:

- Standardizes process dispatch, return code validation, and error messaging across developer tooling utilities
- Eliminates duplicate subprocess execution logic and divergent platform-specific branching across utility modules
- Centralizes environment interaction and host command isolation within a dedicated internal module

Negative:

- Consumer utility modules become tightly coupled to the public interface of airflow_breeze.utils.run_utils
- Refactoring internal process execution mechanisms requires backward compatibility across all consuming utilities

## Alternatives

- Direct ad-hoc subprocess invocations across individual utility modules (rejected)
  Rejected because: Causes fragmented error handling, inconsistent console logging, and duplicated process execution logic across utilities
  When valid: Standalone single-file scripts operating outside the main codebase architecture
- External third-party task execution framework adoption (rejected)
  Rejected because: Introduces unnecessary external dependency overhead when an internal shared module fulfills project execution requirements
  When valid: Complex multi-node distributed workflow orchestration where local subprocess management is insufficient

## Risks

- Breaking changes in airflow_breeze.utils.run_utils can cascade into failures across all dependent utility modules
  Mitigation: Maintain comprehensive unit and integration test suites for the execution module to prevent regressions across consumers
  Owner: Engineering Team
- Centralized execution interfaces may insufficiently support unique subprocess requirements such as custom stream redirection or unusual timeouts
  Mitigation: Ensure execution utility functions expose configurable parameters for timeouts, working directories, environment variables, and stream handling
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
- Utility modules requiring command execution or process management must import and invoke functions from airflow_breeze.utils.run_utils rather than standard library execution primitives
- Host environment detection routines must expose structured domain representations such as enumerations to avoid unstructured string matching in consuming modules

## Continuation Context

Verify commands:

- grep -rn "airflow_breeze.utils.run_utils" .
- find . -type f -exec grep -H "airflow_breeze.utils.run_utils" {} +

Accept when:

- All utility modules dispatching external process executions import and use airflow_breeze.utils.run_utils
- No direct unmanaged process execution invocations exist in consumer utility modules
- The project test suite passes when verifying system command and environment utility behaviors

## Enforcement

- Verified by: Automated continuous integration checks inspecting imports across utility modules
- Verified by: Peer code review validating adherence to shared utility module boundaries
- Violation handling: Code review rejection for any new utility module introducing unmanaged process execution calls
- Violation handling: Mandatory refactoring of non-compliant execution logic to route through airflow_breeze.utils.run_utils
- Exception process: Submit an architectural review request detailing why airflow_breeze.utils.run_utils cannot support the specific process execution requirements
- Exception process: Require approval from the maintainers before introducing alternative execution logic
