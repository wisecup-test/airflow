---
version: 2
name: 50aaaa83-ff45-4dd1-a68f-99abd21336f8-ttlcache-and-lrucache-in-memory-caching-for-primary-datastore-access-shared-memory-caches-accessed-across-concurrent
description: "TTLCache and LRUCache In-Memory Caching for Primary Datastore Access: Shared Memory Caches Accessed Across Concurrent"
metadata:
  actual:
    adr_id: 50aaaa83-ff45-4dd1-a68f-99abd21336f8
    status: accepted
    candidate_group_id: 89bddde6-c678-4ce7-9990-40869c530890
    evidence:
      source: deep
  scope:
    root: airflow-core
    paths:
      - src/airflow/**
---

# TTLCache and LRUCache In-Memory Caching for Primary Datastore Access: Shared Memory Caches Accessed Across Concurrent

Date: 2025-05-18
Deciders: Detection Pipeline (automated)

## Scope Paths

Root: `airflow-core`

Applies to:
- `src/airflow/**`

## Context

- Primary relational datastores experience significant query pressure when recurring scheduler loops, dependency evaluation engines, and API endpoints repeatedly query identical entity records.
- Deserialization of complex data structures and repeated relational joins for metadata attributes introduce non-trivial computational overhead when performed on every read.
- Core models and dependency evaluators require low-latency access to frequently consulted attributes without incurring continuous database round-trips.

## Problem Statement

High-frequency scheduler loops, dependency evaluation engines, and API services generate redundant read queries against primary relational datastores for relatively static or slowly changing entity definitions, resulting in database connection pool saturation, query contention, and elevated latency.

## Decision

- **R-PD-001** MUST: Shared in-memory caches accessed across concurrent execution contexts MUST synchronize read and write mutations using an explicit lock parameter or thread-safe synchronization wrapper.

## Policy Block

In scope:

- Primary datastore access layers, ORM entity queries, and model deserialization routines evaluated in high-frequency execution loops.
- Repeated queries for static or slowly mutating configuration, schema definitions, and entity ownership mappings.

Out of scope:

- Transactional mutation operations, state transitions, and primary datastore write paths that require strict immediate persistence.
- Distributed caching requirements spanning multiple distinct runtime process nodes requiring external shared cache infrastructure.

## Rationale

- Empirical evidence confirms widespread adoption of bounded in-memory cache primitives across core models, dependency evaluators, and serialization routines.
- Size-bounded and time-bounded caching protects process memory while shielding relational datastores from query amplification.
- In-process caching eliminates network serialization overhead associated with remote cache services for local model evaluation.

## Consequences

Positive:

- Shields the primary relational datastore from redundant query amplification during high-frequency evaluation cycles.
- Drastically reduces operational latency for repeated reads of schema definitions, ownership records, and serialized structures.
- Bounds local process memory utilization through explicit least-recently-used and time-based eviction constraints.

Negative:

- Introduces the possibility of transient read staleness when underlying primary datastore records mutate concurrently.
- Consumes local process heap memory proportional to the configured maximum cache size limits.
- Requires synchronization safeguards and validation logic to manage cache coherency across concurrent execution contexts.

## Alternatives

- Direct primary datastore queries on every execution cycle without in-process caching (rejected)
  Rejected because: Repeated database queries across high-frequency execution loops cause severe datastore contention, connection pool exhaustion, and query latency spikes.
  When valid: Valid only in strictly low-throughput administrative commands where data change frequency exceeds read frequency.
- Unbounded in-memory mapping structures without eviction or expiry (rejected)
  Rejected because: Unbounded memory structures risk memory leaks and process crashes as unique entity identifiers accumulate over time.
  When valid: Valid only for strictly finite, statically enumerated program constants determined at compilation or startup.
- Dedicated external distributed caching cluster (deferred)
  Rejected because: Introduces external infrastructure complexity, network hop latency, and serialization overhead for reads that can be handled within local process memory.
  When valid: Valid when cross-process distributed invalidation consistency across horizontally autoscaled stateless services is strictly required.

## Risks

- Data staleness where an in-memory cache serves out-of-date model representations after concurrent database updates.
  Mitigation: Enforce bounded time-to-live expiration alongside hash-based staleness checks and cache invalidation signals on write operations.
  Owner: engineering team
- Process memory exhaustion caused by oversized or unevicted cache stores.
  Mitigation: Enforce strict bounded capacity constraints on all cache structures.
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
- Configure time-to-live intervals based on the tolerance for staleness of the specific entity domain, matching write frequencies to expiry thresholds.
- Attach explicit thread locks to shared cache structures when concurrent asynchronous workers or threads access the cached data layer.
- Structure cache entry wrappers to encapsulate validation metadata, including entity content hashes and monotonic verification timestamps.

## Continuation Context

Verify commands:

- Discover the repository test runner from the project manifests and execute unit test suites covering the datastore caching layers.
- Discover and run static analysis linters across the codebase to ensure all cache definitions define explicit maxsize limits.

Accept when:

- Primary datastore query invocations in recurring execution loops resolve from in-memory caches upon repeated lookups.
- All cache declarations configure explicit maximum size constraints and, where required for mutable records, explicit expiration intervals.
- Cache telemetry confirms operational hits and misses without unbounded memory growth or process memory exhaustion.

## Enforcement

- Verified by: Automated linting rules detecting unbounded in-memory collection growth and missing capacity parameters.
- Verified by: Code review verification for all new primary datastore read paths in recurring execution loops.
- Verified by: Integration test suites asserting that repeated entity retrievals do not increment primary datastore query counters.
- Violation handling: Code reviews reject un-cached repetitive datastore queries in high-frequency execution paths.
- Violation handling: Pull requests introducing unbounded caching collections are blocked until bounded capacity and eviction policies are configured.
- Exception process: Submit an architectural exception request documenting why direct primary datastore round-trips or unbounded storage are required.
- Exception process: Obtain sign-off from the technical leads following query impact analysis and benchmark evaluations on datastore capacity.
