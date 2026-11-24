---
version: 2
name: cross-cutting-every-invocation-pool-apply-async-7094
description: "Worker Pool Asynchronous Task Dispatch via pool.apply_async: Every Invocation Pool Apply Async Pass"
metadata:
  actual:
    adr_id: 7094ceb9-42a3-4e86-ad06-0c61ec65e326
    status: accepted
    candidate_group_id: 1e1d545a-4848-4f5e-a200-563ae810bc7b
    evidence:
      source: deep
  scope:
    root: dev
    paths:
      - breeze/src/airflow_breeze/**
---

# Worker Pool Asynchronous Task Dispatch via pool.apply_async: Every Invocation Pool Apply Async Pass

Scope: applies to `dev/breeze/src/airflow_breeze/**`.

These rules are ALWAYS ACTIVE for:
- Subcommand workflows and integration pipelines executing parallel task matrixes, batch artifact builds, or multi-target test suites.

### Rules

- **R-IP-001** MUST: Every invocation of pool.apply_async MUST pass task execution parameters through an explicit keyword dictionary containing an indexed output buffer destination.

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
