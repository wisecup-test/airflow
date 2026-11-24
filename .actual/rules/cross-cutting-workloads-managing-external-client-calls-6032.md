---
version: 2
name: cross-cutting-workloads-managing-external-client-calls-6032
description: "Worker Pool Asynchronous Task Dispatch via pool.apply_async: Workloads Managing External Client Calls Multi"
metadata:
  actual:
    adr_id: 603202e5-77e2-49e5-897e-e3a77b9c7af5
    status: accepted
    candidate_group_id: 1e1d545a-4848-4f5e-a200-563ae810bc7b
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Worker Pool Asynchronous Task Dispatch via pool.apply_async: Workloads Managing External Client Calls Multi

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- Subcommand workflows and integration pipelines executing parallel task matrixes, batch artifact builds, or multi-target test suites.

### Rules

- **R-IP-001** SHOULD: Workloads managing external client calls or multi-phase environment provisioning through pool.apply_async SHOULD configure explicit retry limits within task parameters to handle parallel resource contention.

### Verify

- Discover and run the project static analysis and test validation suites verifying worker pool task dispatch compliance.
- Execute concurrency integration checks by discovering repository test runner scripts and validating parallel execution workflows.

**Accept when:**

- All background integration tasks dispatch asynchronously through worker pool interfaces without writing directly to unbuffered global console streams.
- Concurrency verification suites pass with all pooled worker tasks returning verified termination states.

<enforcement>
Claude Code MUST NOT skip or defer verification.
- Verified by: Automated static analysis checks detecting uncoordinated process instantiation or direct unbuffered console streaming during parallel execution.
- Verified by: Peer code review verifying pool.apply_async usage with structured keyword arguments and isolated per-task output buffers.
- Violation handling: Pull requests containing uncoordinated process spawning or missing output isolation are blocked until aligned with pool dispatch conventions.
- Exception process: Exceptions must be submitted via architectural review with documented rationale justifying why pooled execution or isolated outputs are infeasible.
</enforcement>
