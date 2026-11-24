---
version: 2
name: 707e77b0-5a57-472b-a1f9-eaa9287d6b81-adoption-of-airflow-providers-standard-operators-bash-for-workflow-task-execution-workflows-not-define-custom-command-execution
description: "Adoption of airflow.providers.standard.operators.bash for Workflow Task Execution: Workflows Not Define Custom Command Execution"
metadata:
  actual:
    adr_id: 707e77b0-5a57-472b-a1f9-eaa9287d6b81
    status: accepted
    candidate_group_id: 91eb57ca-62cd-42b7-8749-d1131cb8b266
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - airflow_perf/dags/**
---

# Adoption of airflow.providers.standard.operators.bash for Workflow Task Execution: Workflows Not Define Custom Command Execution

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `dev`

Applies to:
- `airflow_perf/dags/**`

## Context

- Workflow definitions require standardized, reusable operators to execute script-based tasks within orchestrated execution graphs.
- Decoupling operator implementations into standard provider packages isolates execution logic from orchestrator core definitions.
- Automated performance evaluation and workflow DAGs require uniform task execution semantics and consistent upstream dependency management.

## Problem Statement

Workflow definitions requiring command execution risk inconsistent execution semantics, fractured dependency chaining, and tight coupling if disparate execution mechanisms or bespoke task scripts are used instead of a unified standard provider operator.

## Decision

- **R-LM-001** MUST_NOT: Workflows MUST NOT define custom command execution abstractions when the requirements are satisfied by airflow.providers.standard.operators.bash.

## Policy Block

In scope:

- Workflow DAG definitions defining executable script or command tasks.
- Performance benchmarking and workflow orchestration task graphs.

Out of scope:

- Tasks requiring specialized remote service execution operators or non-shell runtime targets.
- Direct orchestrator core framework internals that do not declare user-facing workflow tasks.

## Rationale

- Standardizing task execution on airflow.providers.standard.operators.bash ensures consistent execution lifecycles, exit code handling, and process isolation across workflow tasks.
- Using standard provider operators separates task execution logic from orchestrator engine internals, ensuring clean provider boundary modularity.
- Adopting explicit dependency protocol methods like set_upstream ensures clear graph topologies that can be validated statically and scheduled reliably.

## Consequences

Positive:

- Uniform execution semantics across command-driven workflow tasks.
- Clear separation between task execution mechanics and workflow scheduling logic.
- Simplified dependency management and modular provider maintenance.

Negative:

- Process overhead associated with spawning subshell environments for individual task invocations.
- Requirement to explicitly manage and sanitize environment variables passed into task execution contexts.

## Alternatives

- Bespoke subshell execution within inline scripting operators (rejected)
  Rejected because: Inline subshell execution scatters command invocation logic, bypasses standardized operator monitoring, and complicates dependency graph declaration.
  When valid: When tasks require tight coupling with in-process interpreter state that cannot execute in isolated subprocesses.
- Containerized task operators (deferred)
  Rejected because: Containerized execution adds container runtime overhead and infrastructure prerequisites unnecessary for local workflow benchmark tasks.
  When valid: When workloads require strict multi-tenant filesystem isolation or specialized system library dependencies.

## Risks

- Unsanitized dynamic parameter expansion in shell command strings could lead to execution errors or security vulnerabilities.
  Mitigation: Validate and sanitize all runtime configuration values before passing them into task execution arguments.
  Owner: engineering team
- Subprocess resource contention during high-concurrency DAG execution.
  Mitigation: Configure orchestrator concurrency limits and calibrate worker execution capacity against workload benchmarks.
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
- Discover the project manifest and lock artifact to verify the available operator arguments and interfaces exposed by the standard provider package.
- Construct task dependency trees using explicit relationship protocols to maintain readability and predictable scheduler parsing.

## Continuation Context

Verify commands:

- Discover and run the project test suite to verify workflow DAG integrity and task parsing.
- Discover and execute the static analysis and linting checks configured in the repository to ensure operator import compliance.

Accept when:

- All workflow DAG definitions successfully parse without import or configuration errors.
- Static analysis confirms task definitions adhere to standard provider operator patterns and relationship protocols.

## Enforcement

- Verified by: Automated continuous integration checks executing DAG parsing tests and static linting.
- Verified by: Peer code review evaluating task operator usage and dependency graph construction.
- Violation handling: Pull requests introducing non-standard command execution mechanisms or ungrounded versions will be blocked until corrected.
- Exception process: Submit an architectural exception request documenting why standard provider operators cannot fulfill task execution requirements.
