---
name: backend-and-data-architecture
description: Use when shaping services, APIs, data stores or integration. Domain-first sequence, boring defaults.
metadata:
  version: "0.1.0"
  status: draft
  owner: maintainer
---

# Back-end and data architecture

A sequence for designing the server side of a system: the simplest design that meets today's
measured requirements and stays cheap to change as they grow. The general decision method belongs
to `architecture-decision-making`, source vetting to `architecture-research-and-verification`, and
code-level principles to `engineering-principles`. This skill applies them to one domain.

## When to use

- Starting a new back end, or deciding whether to split, merge or re-platform an existing one.
- Choosing an API style, a versioning policy, a database, a queue or log, or a workflow engine.
- Designing cross-service consistency, retries, multi-tenant isolation, or a retrieval (RAG) store.
- Reviewing a design that does any of the above.

Not for changes inside one existing module and store: that is ordinary engineering.

## Decision sequence

Work in this order: later decisions depend on earlier ones, and topology is the one most often
taken too early. Record each significant choice with its rejected options and accepted
downsides, attach the evidence that settled it, and state confidence as low, medium or high
using the shared rubric in `architecture-research-and-verification`.

1. **Domain model and ownership boundary.** Before technology, name the domain model, its
   vocabulary and who owns it. Escalate to subdomain classification (build the core, buy or rent
   the generic), event storming and a context map with named relationships only when vocabulary,
   integration or team boundaries are contested. *Violation:* boundaries drawn by technical layer
   ("the database service") or by org chart alone. *When not to escalate:* one model and one team,
   such as a small CRUD tool.
2. **Module boundaries inside a modular monolith (the default).** Each context becomes a module
   with an explicit public interface, owned data and a one-way dependency graph, enforced by a
   build-time check. Score candidate couplings by strength, distance and volatility. Keep strongly
   coupled, frequently changing pairs together. *Violation:* façades over a cyclical dependency
   graph (fake modularity).
3. **Deployment topology, as a separate and later decision.** Extract a module into a service only
   when a named force applies: independent teams blocked on one release train, different scaling or
   runtime profiles, regulatory isolation, or a distinct SLA. Price it: independent deploys bought
   with remote-call failure, eventual consistency and operational load. For serverless, model
   per-invocation and per-transition cost at expected volume. Migrate with a strangler fig;
   splitting the data is the hard part.
4. **API style and evolution contract.** Compare styles on consumer diversity, streaming, caching,
   browser reach, schema and evolution tooling, latency, authorization granularity and governance.
   Defaults, with when-not cases in the guide: resource-oriented HTTP with a machine-readable spec
   for public and partner APIs, typed RPC for internal low-latency or streaming calls, a graph
   query layer for many client shapes over many back ends, and an async contract for events. Lint
   against one house style guide. Never break clients: additive change, tolerant readers,
   expand-then-contract, contract tests and breaking-change checks in CI. Assume every observable
   behaviour is depended on. Version only for a true break, in one style. Use standard
   problem-details errors.
5. **Data ownership and store selection.** Each module or service owns its data. Others reach it
   through an API or events, never through shared tables. Start with the relational database you
   already run and map each need to a built-in feature: documents, full-text search, vectors, a
   transactional job queue, partitioning. An embedded database fits one writer host with the data
   next to the app. Every additional store must show a measured gap on your workload, and its
   operational cost is charged as an innovation token.
6. **Data lifecycle and governance.** For each data class (such as personal, financial or derived),
   record: classification and owner; retention, deletion and legal hold; lineage to every copy
   (replicas, caches, indexes, events, analytics, exports, backups); schema evolution with a
   migration and rollback path; encryption and key ownership; data-quality checks; residency;
   backup with a tested restore. *Violation:* account deletion that leaves the subject in a search
   index, event log or export. *When lighter:* an internal tool holding no personal or regulated
   data needs one line per class, not a programme.
7. **Consistency model and integration.** State the guarantee each flow needs (linearizable,
   serializable, read-your-writes, eventual), not with a CAP label. Name which "event-driven" you
   mean (notification, state transfer, event sourcing, CQRS); confine the last two to one bounded
   context. Across services, use sagas with compensations, a transactional outbox (or change-data
   capture) instead of dual writes, and idempotent consumers with caller-supplied request IDs.
   Assume at-least-once delivery. Use a queue to distribute work and a log for ordered, replayable
   history with many readers. For long-running processes, start with a database-backed state
   machine with idempotent steps, leases and crash-recovery tests. Move to a durable-execution
   engine when histories, timers, retries, compensation, visibility or operational scale exceed
   what that machine handles under test. *Violation:* cron plus status columns with no lease and no
   test of a crash mid-step.
8. **Resilience.** On every remote call: a timeout derived from downstream latency percentiles, a
   propagated deadline, retries at one layer only, capped exponential backoff with jitter, a retry
   budget, idempotency on mutations, and load shedding with an explicit "retry later" signal. Bound
   every queue, exercise any fallback, and know the circuit-breaker caveats. Treat a cache as a
   dependency: ask whether the database survives a cold cache.
9. **Multi-tenancy.** Choose silo, pool or bridge per resource and per tier, driven by isolation,
   compliance, noisy-neighbour risk and cost. In a pool, enforce tenant scoping in the data layer
   (for example row-level security), not by convention. Decide early: retrofitting isolation is
   expensive.
10. **Application security.** Draw trust boundaries on the topology and data flows, then decide:
    how end-user identity propagates across hops (a verified token, not a header any caller can
    set); how services prove identity (workload identity, short-lived credentials, no shared static
    secrets); the authentication method; the authorization policy model and its enforcement point
    (deny by default, per object and per function, in the service that owns the data); where
    secrets and keys live and how they rotate; abuse controls (rate limits, quotas, payload and
    query-cost limits); which security events are audited; encryption per data class. Look up the
    current OWASP ASVS and API Security Top 10 at task time; select the ASVS level by application
    risk. Verify with a threat model revisited at each significant change and tests of denied
    paths. *Violation:* authorization only at the gateway, or object IDs accepted without an
    ownership check.
11. **Observability.** Instrument with the vendor-neutral standard and its semantic conventions.
    Emit wide, structured, correlated events with high-cardinality fields, within limits: classify
    telemetry like other data, keep secrets and unnecessary personal data out, and set a
    cardinality and cost budget, sampling and retention. Alert on SLOs over the golden signals.
12. **AI-era data.** Put vectors in the existing database first. Exact k-NN has recall 1.0 relative
    to exhaustive search under the chosen embedding and distance at the same k; it does not
    guarantee semantic relevance. Approximate indexes trade that recall for speed. Build an
    evaluation set with quality, latency and cost thresholds first. Treat hybrid lexical plus dense
    retrieval, chunk contextualisation, reranking and long-context or no retrieval as candidates;
    keep what passes. Move to a dedicated engine only when a benchmark on your data, filters and
    recall target shows the gap. Prefer a fixed workflow to an autonomous agent when it suffices.

Details, options and sources for each step: [references/decision-guides.md](references/decision-guides.md).
Consistency, isolation and failure-mode background: [references/distributed-systems-essentials.md](references/distributed-systems-essentials.md).

## Simplicity defaults and what earns complexity

| Default | Earned by |
|---|---|
| One deployable, modular inside | A named force from step 3, with team and ops capacity to pay for it |
| Existing relational DB for everything | A measured gap on your workload (napkin maths, then a spike) |
| Synchronous call inside the process | Fan-out, temporal decoupling, or a cross-context boundary |
| Database-backed state machine | Histories, timers, compensation or visibility it cannot handle under test |
| Additive API change | A true semantic break that cannot be expanded and contracted |
| Pooled tenancy with enforced scoping | Compliance, a premium isolation tier, or measured noisy-neighbour harm |
| Vectors in the existing DB | Scale, QPS or isolation needs shown by your own benchmark |

The value protected is ease of change at low operating cost. A violation looks like a component
whose justification is "we might need it" or "big company X uses it". When not to apply: the novel
choice is the differentiator, or a one-way-door decision (data model of record, public contract,
tenant isolation) is cheaper to get right now than to migrate later.

## Anti-patterns

- **Distributed monolith.** Services that deploy together, share a database, or chain synchronous
  calls. You pay every distribution cost and get no autonomy.
- **Exactly-once assumed end to end.** Broker guarantees stop at the broker's edge.
- **Retry storms.** Retries at every layer multiply load; without jitter they synchronise
  clients. Both can sustain a metastable failure after the trigger has gone.
- **Database chosen from a CAP label or a marketing claim**, without reading independent tests.
- **"Harmless" breaking changes**: new enum values, reordered fields, changed timing.
- **Authorization at the edge only**, with services trusting whatever reaches them.
- **Deletion that misses copies**: backups, indexes, events and exports outside the lifecycle.

## Check current state

Look up volatile facts at decision time and cite the date checked (sources listed 2026-11-19):
database safety analyses (version tested, fixes since), popularity (not fitness), technique
radars, ANN benchmarks (rerun on your data), vector index types, HTTP standards status,
telemetry stability, quotas and pricing, API guide status, the current OWASP ASVS and API
Security Top 10 editions, and your database's docs for the version you run.

URLs and what to verify: [references/decision-guides.md](references/decision-guides.md#check-current-state).

## Links

- `architecture-decision-making`: quality scenarios, options, reversibility, decision records.
- `architecture-research-and-verification`: source grading, benchmarks, napkin maths, citations,
  and the shared low / medium / high confidence rubric.
- `cloud-and-infrastructure-architecture`: network, cloud identity and backup foundations.
- `engineering-principles`: module depth and one-way boundaries (EP-03), least complexity
  (EP-06), wrong states hard to represent (EP-07).
- [references/reading-list.md](references/reading-list.md): tiered reading with year, type and URL.
