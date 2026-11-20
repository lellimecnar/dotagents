# Decision guides

One table per decision in the SKILL.md sequence. Columns: options, the forces that favour each, when
not to choose it, and sources. URLs checked 2026-11-19 with a scripted browser request (one retry): all resolve. † marks publisher pages that refuse scripted clients (HTTP 403); each was confirmed live by a recent web-archive capture returning 200. Rows are defaults with when-not cases, not fixed
mappings.

## 1. Domain model and ownership boundary

| Option | Forces | When not | Sources |
|---|---|---|---|
| Name the domain model, its vocabulary and its owner (always the first step) | Every system | — | https://martinfowler.com/bliki/BoundedContext.html |
| Escalation: strategic DDD with bounded contexts, subdomains classified core / supporting / generic | Vocabulary, integration or team boundaries are contested. Domain still being discovered. Build/buy/outsource choices are needed. Several teams | A small CRUD tool with one model and one team. Name the model and move on | https://martinfowler.com/bliki/BoundedContext.html ; https://www.domainlanguage.com/ddd/reference/ ; https://oreilly.com/library/view/learning-domain-driven-design/9781098100124/part03.html † |
| Context map with named relationships (partnership, customer/supplier, conformist, anti-corruption layer, open host / published language) | Integration with legacy or third-party models. Team dependencies need to be explicit | A single context | https://github.com/ddd-crew/context-mapping |
| Domain analysis to service boundaries, step by step | You need a repeatable walk-through for a team | Boundaries are already proven in production | https://learn.microsoft.com/en-us/azure/architecture/microservices/model/domain-analysis |

Method: name the model and owner → if boundaries are contested, event storming or domain analysis
→ classify subdomains → context map → score coupling → each context starts as a module.

## 2. Module boundaries

| Option | Forces | When not | Sources |
|---|---|---|---|
| Modular monolith with tool-enforced boundaries | One or a few teams. Strong consistency wanted. Low ops budget | See topology table | https://www.kamilgrzybek.com/blog/posts/modular-monolith-primer ; https://github.com/kgrzybek/modular-monolith-with-ddd ; https://shopify.engineering/enforcing-modularity-rails-apps-packwerk ; https://spring.io/projects/spring-modulith |
| Coupling scored by integration strength × distance × volatility | Choosing which candidates to merge or split | Do not aim for zero coupling. Aim for balance | https://coupling.dev/ ; https://connascence.io/ |
| Decompose by decisions likely to change (information hiding) | Every boundary | — | https://dl.acm.org/doi/10.1145/361598.361623 † |

Check the dependency graph, not just interfaces: "A dense, cyclical dependency graph undermines
the whole idea of Componentization" https://shopify.engineering/shopify-monolith.

## 3. Deployment topology

| Option | Forces | When not | Sources |
|---|---|---|---|
| Modular monolith (default) | Domain still moving. Few teams. Low ops capacity | Very different scaling or runtime profiles. Regulatory isolation. Many teams blocked on one release train | https://martinfowler.com/bliki/MonolithFirst.html ; https://martinfowler.com/bliki/MicroservicePremium.html ; https://shopify.engineering/deconstructing-monolith-designing-software-maximizes-developer-productivity |
| Microservices | Many autonomous teams on stable contexts. Different cadences or SLAs. Mature platform | Unclear boundaries (distributed monolith). Small team. Coordinated multi-service deploys. Fashion. Segment merged "over 140 services into a single service" | https://martinfowler.com/articles/microservice-trade-offs.html ; https://www.thoughtworks.com/radar/techniques/microservice-envy ; https://www.twilio.com/en-us/blog/developers/best-practices/goodbye-microservices ; https://www.uber.com/us/en/blog/microservice-architecture/ |
| Serverless functions / managed orchestration | Spiky, low or unpredictable traffic. Glue between managed services. Scale to zero | High-frequency, chatty, data-heavy pipelines billed per transition. Stateful or low-latency coordination | https://arxiv.org/abs/1902.03383 ; https://arxiv.org/abs/1812.03651 ; https://web.archive.org/web/20240325042615/https://www.primevideotech.com/video-streaming/scaling-up-the-prime-video-audio-video-monitoring-service-and-reducing-costs-by-90 |
| Logical monolith, physical distribution | One codebase and atomic rollouts, runtime decides co-location | Polyglot teams needing independent release ownership | https://sigops.org/s/conferences/hotos/2023/papers/ghemawat.pdf |

Migration: strangler fig https://martinfowler.com/bliki/StranglerFigApplication.html ; data
splitting patterns https://samnewman.io/books/monolith-to-microservices/ ; do not distribute
objects https://martinfowler.com/articles/distributed-objects-microservices.html.
Counter-view (start with services when the domain is well understood and teams exist from day one):
https://martinfowler.com/articles/dont-start-monolith.html. Decide by domain certainty and team
count, not dogma.

## 4. API style and evolution

Compare candidates on consumer diversity, streaming, caching, browser reach, schema and evolution
tooling, latency, authorization granularity (per-field authorization is harder in a graph layer)
and governance (who owns the spec and lint rules). The rows below are common defaults; a when-not
case overrides them.

| Option | Forces | When not | Sources |
|---|---|---|---|
| Resource-oriented HTTP + OpenAPI | Public and partner APIs, cacheable reads, broad tooling | High-rate internal streaming | https://google.aip.dev/121 ; https://opensource.zalando.com/restful-api-guidelines/ ; https://ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm ; https://www.rfc-editor.org/rfc/rfc9110 |
| gRPC / Protobuf | Internal low-latency calls, streaming, built-in deadlines and cancellation | Browser-first public APIs without a gateway. No schema discipline | https://grpc.io/docs/what-is-grpc/core-concepts/ ; https://protobuf.dev/programming-guides/dos-donts/ |
| GraphQL | Many client shapes over many back ends. Versionless evolution | Simple CRUD, server-to-server, or no budget for query-cost limits and per-field authorisation | https://graphql.org/learn/best-practices/ |
| Async events (AsyncAPI) | Fan-out, temporal decoupling, cross-context integration | Caller needs an immediate answer. Workflow needs visibility (use orchestration) | https://www.asyncapi.com/docs |

| Evolution technique | Use | Sources |
|---|---|---|
| Additive change + tolerant reader + expand/contract | Default for every change | https://martinfowler.com/bliki/TolerantReader.html ; https://martinfowler.com/bliki/ParallelChange.html |
| Compatibility definitions (source, wire, semantic) | Reviewing whether a change breaks | https://google.aip.dev/180 ; https://www.hyrumslaw.com/ |
| Date-named rolling versions + compatibility layer | Long-lived public API with many integrators | https://stripe.com/blog/api-versioning |
| Major version in path or package | Clear, infrequent breaks | https://google.aip.dev/185 |
| Consumer-driven contract tests, schema breaking-change checks | CI gate | https://martinfowler.com/articles/consumerDrivenContracts.html ; https://docs.pact.io/ ; https://buf.build/docs/breaking/overview |
| Problem Details errors; Sunset header | Error format; announcing retirement | https://www.rfc-editor.org/rfc/rfc9457 ; https://www.rfc-editor.org/rfc/rfc8594 |
| Caller-supplied request ID on mutations | Safe retries | https://google.aip.dev/155 ; https://aws.amazon.com/builders-library/making-retries-safe-with-idempotent-apis/ |

All three versioning styles work. Consistency within one organisation matters more than the choice.

## 5. Data store selection

| Option | Forces | When not | Sources |
|---|---|---|---|
| Existing relational DB, features mapped to needs (JSON, full-text, vectors, `SKIP LOCKED` queues, partitioning) | Default. One backup, ACL and on-call story | A measured gap on your workload. Note that "Postgres for everything" is advocacy | https://www.amazingcto.com/postgres-for-everything/ ; https://www.postgresql.org/docs/current/sql-select.html ; https://www.postgresql.org/docs/current/textsearch-intro.html ; https://github.com/pgmq/pgmq ; https://github.com/pgvector/pgvector |
| Embedded DB (SQLite) + streamed replication | One writer host, edge, read-heavy, per-tenant files | Many writers on many hosts sharing a database | https://www.sqlite.org/whentouse.html ; https://litestream.io/ ; https://fly.io/blog/all-in-on-sqlite-litestream/ ; https://blog.pecar.me/sqlite-prod/ |
| Specialised store (search engine, columnar analytics, wide-column, cache) | Search relevance at scale, very high write rate, global distribution, analytics, hot keys | No measurement yet. Each one costs an innovation token | https://mcfunley.com/choose-boring-technology |
| Database per service | Services that must evolve independently | A modular monolith, where schema-per-module with enforced access is enough | https://microservices.io/patterns/data/database-per-service.html |

Method: write down access patterns, consistency needs, volume and growth, latency SLO and ops
capacity. Then confirm the existing database's features cover those needs on the version and
plan you run, measure, and justify each addition by the measured gap. For any distributed database, read its
Jepsen report and work out its PACELC behaviour https://jepsen.io/analyses. Polyglot costs (dual
writes, sync pipelines, more restore targets) are mitigated with an outbox or CDC from one system
of record https://microservices.io/patterns/data/transactional-outbox.html.

## 6. Data lifecycle and governance

Record one row per data class (for example personal, financial, operational, derived). Columns
below are the decisions; a store-level answer is not enough when copies exist elsewhere.

| Decision | Options and forces | When lighter / caveat | Sources |
|---|---|---|---|
| Classification and owner | Sensitivity class, legal basis where relevant, accountable owner | One line per class for an internal tool with no personal or regulated data | https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-resources/accountability-and-governance/guide-to-accountability-and-governance/data-protection-by-design-and-by-default/ |
| Retention, deletion, legal hold | Retention period per class; deletion that reaches replicas, caches, search indexes, event logs, analytics, exports and backups within a stated window; a legal-hold override. Append-only logs need a plan (for example per-subject keys destroyed on deletion, or compaction) | Data with no retention obligation still needs an owner and a deletion path | same |
| Lineage | Source of record and every derived copy, recorded so deletion, correction and impact analysis can find them | A single store with no derived copies | https://openlineage.io/ |
| Schema evolution and migration | Expand/contract migrations, backfills that can resume, a tested rollback path or forward-fix plan | — | https://martinfowler.com/bliki/ParallelChange.html |
| Encryption and key ownership | Who owns, rotates and can revoke keys per class; separation of key and data administration for the highest classes | — | https://cheatsheetseries.owasp.org/cheatsheets/Key_Management_Cheat_Sheet.html |
| Data quality | Validation at ingress, reconciliation for derived copies, ownership of fixes | — | — |
| Residency | Where each class may be stored and processed, including backups and telemetry | No residency obligation | — |
| Backup and restore | Backup per class with a restore tested against the RPO/RTO; platform backup strategy belongs to `cloud-and-infrastructure-architecture` | — | https://www.backblaze.com/blog/the-3-2-1-backup-strategy/ |

## 7. Consistency and integration

| Option | Forces | When not | Sources |
|---|---|---|---|
| Event notification | "Something happened, go look." Lowest coupling | End-to-end flow must be visible | https://martinfowler.com/articles/201701-event-driven.html |
| Event-carried state transfer | Consumers keep working when the source is down. Read-heavy consumers | Staleness unacceptable. Schema becomes a public contract | https://martinfowler.com/articles/201701-event-driven.html |
| Event sourcing | Audit, temporal queries, rebuilding projections are core needs | CRUD domains. Hard event-schema evolution and replay | https://martinfowler.com/eaaDev/EventSourcing.html ; https://learn.microsoft.com/en-us/azure/architecture/patterns/event-sourcing |
| CQRS | Read and write models truly diverge inside one context | System-wide | https://martinfowler.com/bliki/CQRS.html ; https://learn.microsoft.com/en-us/azure/architecture/patterns/cqrs |
| Saga (choreography or orchestration) | Multi-service business transaction | The work fits in one database transaction; keep it there | https://www.cs.cornell.edu/andru/cs711/2002fa/reading/sagas.pdf ; https://microservices.io/patterns/data/saga.html ; https://queue.acm.org/detail.cfm?id=3025012 † |
| Transactional outbox / CDC | Any DB write plus publish | — | https://microservices.io/patterns/data/transactional-outbox.html |
| Idempotent consumers, idempotency keys | Always: assume at-least-once | — | https://stripe.com/blog/idempotency ; https://brandur.org/idempotency-keys ; https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it/ |

| Need | Starting candidate (confirm against the existing stack and the versions you run) | Notes / sources |
|---|---|---|
| Work distribution, per-message ack, retries, DLQ | Queue | Bound the backlog (TTL, LIFO under overload, separate queues) https://aws.amazon.com/builders-library/avoiding-insurmountable-queue-backlogs/ |
| Many consumers, replay, per-key ordering | Log | Partition key = ordering scope https://www.rabbitmq.com/docs/streams ; https://kafka.apache.org/documentation/ ; https://web.archive.org/web/20180201201617/https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying |
| Modest volume, transactional enqueue | Database table + `SKIP LOCKED` | Outbox for free. Watch vacuum and bloat https://www.postgresql.org/docs/current/sql-select.html |
| Multi-step process, modest history and scale, no workflow engine already run | Database-backed state machine | Idempotent steps, leases, crash-recovery tests. Outbox for side effects https://www.postgresql.org/docs/current/sql-select.html |
| Histories, timers, retries, compensation, visibility or operational scale beyond what the tested state machine handles | Durable-execution / workflow engine | https://docs.temporal.io/temporal ; https://temporal.io/blog/what-is-durable-execution. Model per-transition cost (Prime Video case above) |

## 8. Resilience (per remote call)

| Mechanism | Rule | When not / caveat | Sources |
|---|---|---|---|
| Timeout | Derive from the acceptable false-timeout rate (for example downstream p99.9) | — | https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/ |
| Deadline propagation | Pass remaining budget downstream | — | https://sre.google/sre-book/addressing-cascading-failures/ |
| Retries | One layer only. Capped exponential backoff with jitter. Token bucket or budget | Four attempts per layer across three retrying layers can produce 4³ = 64 attempts; AWS's example uses three attempts across five layers, 3⁵ = 243 | https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/ ; https://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/ ; https://brooker.co.za/blog/2022/02/28/retries.html |
| Load shedding, adaptive throttling | Reject early with 429/503 + Retry-After | — | https://aws.amazon.com/builders-library/using-load-shedding-to-avoid-overload/ ; https://sre.google/sre-book/handling-overload/ |
| Circuit breaker | Fail fast on a dead dependency | Can turn partial failure into full failure in sharded systems | https://learn.microsoft.com/en-us/azure/architecture/patterns/circuit-breaker ; https://brooker.co.za/blog/2022/02/16/circuit-breakers/ |
| Fallback / degradation | Only if continuously exercised and adds no load | Rarely run paths hide bugs | https://aws.amazon.com/builders-library/avoiding-fallback-in-distributed-systems/ ; https://aws.amazon.com/builders-library/caching-challenges-and-strategies/ |
| Cache | Request coalescing, soft/hard TTLs, survive a cold start | Cache as hidden dependency | https://aws.amazon.com/builders-library/caching-challenges-and-strategies/ ; https://www.usenix.org/conference/nsdi13/technical-sessions/presentation/nishtala |
| Cells, shuffle sharding | Bound blast radius | — | https://aws.amazon.com/builders-library/workload-isolation-using-shuffle-sharding/ |

Stability pattern catalogue: https://pragprog.com/titles/mnee2/release-it-second-edition/.

## 9. Multi-tenancy

| Option | Forces | When not | Sources |
|---|---|---|---|
| Pool (shared DB, tenant key + row-level security) | Cheapest, simplest | Without enforced scoping and noisy-neighbour controls | https://docs.aws.amazon.com/wellarchitected/latest/saas-lens/silo-pool-and-bridge-models.html ; https://learn.microsoft.com/en-us/azure/architecture/guide/multitenant/approaches/storage-data |
| Bridge (shared compute, schema or DB per tenant) | Middle ground on isolation and cost | Very many small tenants (schema sprawl) | same |
| Silo (stack or account per tenant) | Compliance, premium tiers, per-tenant cost visibility | Low-value tenants. High ops cost | same ; https://docs.aws.amazon.com/wellarchitected/latest/saas-lens/saas-lens.html |

## 10. Application security

Draw trust boundaries on the topology and data-flow diagram first, then make each decision below
per boundary. Look up the current OWASP ASVS and API Security Top 10 editions at task time and
map the requirements that apply; do not rely on remembered category names or numbering.

| Decision | Options and forces | When not / caveat | Sources |
|---|---|---|---|
| Trust boundaries and threat model | Data-flow diagram with boundaries; ask what can go wrong at each, what you will do about it, and whether you did a good job; revisit at each significant change | Size the model to the risk: a one-page model for a small internal tool | https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html ; https://threatmodelingmanifesto.org ; https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats |
| End-user identity propagation | A verified, audience-restricted, short-lived token passed or exchanged at each hop; services validate it rather than trusting the caller | A plain header that any internal caller can set | https://www.rfc-editor.org/rfc/rfc9700 |
| Service / workload identity | Platform-issued workload identity, mutual TLS or signed short-lived credentials; per-request authentication rather than network location | Shared static keys between services; trust by network segment alone | https://spiffe.io/docs/latest/spiffe-about/overview/ ; https://csrc.nist.gov/pubs/sp/800/207/final |
| Authentication | An established identity provider and standard protocol | Custom credential handling | https://owasp.org/www-project-application-security-verification-standard/ |
| Authorization policy and enforcement point | Policy model (role, attribute or relationship based) chosen by sharing semantics; enforced in the service that owns the data; deny by default; object-level and function-level checks | Gateway-only checks; object IDs accepted from the client without an ownership check | https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html ; https://owasp.org/API-Security/ |
| Secrets and keys | A managed secret store, rotation, short lifetimes; no secrets in code, images or logs; key ownership per data class (step 6) | — | https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html ; https://cheatsheetseries.owasp.org/cheatsheets/Key_Management_Cheat_Sheet.html |
| Abuse controls | Rate limits and quotas per identity and tenant, payload and pagination limits, query-cost limits for graph APIs | — | https://owasp.org/API-Security/editions/2023/en/0x11-t10/ (check for a newer edition) |
| Audit events | Who did what to which resource, with outcome and correlation ID; tamper-evident storage; retention per step 6; no secrets or unnecessary personal data | — | https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html |
| Encryption | In transit (including internal hops where the threat model requires), at rest per data class, field-level for the most sensitive fields | — | https://owasp.org/www-project-application-security-verification-standard/ |
| Assurance level | Select the ASVS level from application risk, data sensitivity and expected assurance; ASVS 5.0 says most applications should strive for L2 | Selecting by team or company size | https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x03-What-is-the-ASVS.md |

Verification: the threat model is reviewed when a boundary, data class or identity flow changes,
and automated tests cover denied paths (wrong tenant, wrong object owner, missing scope, expired
credential), not only allowed ones. Network, cloud-identity and platform foundations belong to
`cloud-and-infrastructure-architecture`.

## 11. Observability

| Option | Forces | When not | Sources |
|---|---|---|---|
| OpenTelemetry with semantic conventions | Vendor neutrality, correlation across signals | Check per-signal stability first | https://opentelemetry.io/docs/concepts/signals/ ; https://opentelemetry.io/docs/specs/semconv/ |
| Wide structured events as the single source of truth | Debugging unknown unknowns, high cardinality | Without a telemetry budget: classify telemetry like other data (step 6), keep secrets and unnecessary personal data out, and set cardinality and cost limits, sampling and retention. Compatible with OTel spans that carry rich attributes | https://charity.wtf/p/there-is-only-one-key-difference-between-observability-1-0-and-2-0 |
| SLO alerting on golden signals | Paging | — | https://sre.google/sre-book/monitoring-distributed-systems/ |

## 12. Vector search and retrieval

| Option | Forces | When not | Sources |
|---|---|---|---|
| Vectors in the existing DB | Filtering joined with relational data, transactional freshness, one backup and ACL story. Exact k-NN has recall 1.0 relative to exhaustive search under the chosen embedding and distance at the same k; it does not guarantee semantic relevance. HNSW/IVFFlat trade that recall for speed | Your own benchmark shows a gap | https://github.com/pgvector/pgvector |
| Dedicated vector engine | Very large corpora, very high QPS with tight p99, per-tenant index isolation, managed hybrid features | Not yet measured on your data, filters and recall target | https://ann-benchmarks.com/ ; https://www.percona.com/blog/benchmarking-vector-search-in-six-database-engines |
| Evaluation set with quality, latency and cost thresholds | Any RAG system, before tuning | — | https://arxiv.org/abs/2312.10997 |
| Candidate levers, selected by the evaluation set: hybrid lexical + dense, chunk contextualisation, reranking, long-context or no retrieval | Each lever that clears the thresholds on your data | A lever that improves quality but breaks the latency or cost threshold | https://www.anthropic.com/news/contextual-retrieval ; https://arxiv.org/abs/2312.10997 ; https://arxiv.org/abs/2005.11401 |
| Workflow before agent | Fixed steps suffice | Task genuinely open-ended | https://www.anthropic.com/engineering/building-effective-agents |

Dated vendor result, not a recipe: Anthropic reported contextual retrieval cut failed retrievals
by 49%, and by 67% with reranking, on its own evaluation (2024). The same report says to evaluate
on the actual use case and notes latency and cost trade-offs
https://www.anthropic.com/news/contextual-retrieval.

## Check current state

Sources listed 2026-11-19. Record the date you check each one.

| Question | Where | What to verify |
|---|---|---|
| Does database X provide its claimed consistency and isolation? | https://jepsen.io/analyses ; https://jepsen.io/consistency ; https://github.com/ept/hermitage | Version tested, analysis date, fixes since |
| Popularity and momentum | https://db-engines.com/en/ranking | Ecosystem and hiring signal only. Popularity is not fitness |
| Adopt / trial / hold signals | https://www.thoughtworks.com/radar | Blip history and volume date |
| ANN performance | https://ann-benchmarks.com/ ; vendor benchmarks | Dataset, recall target, filters, hardware, concurrency. Rerun on your data |
| Vector extension index types | https://github.com/pgvector/pgvector | README and changelog for the current version |
| API design rules | https://google.aip.dev/ ; https://opensource.zalando.com/restful-api-guidelines/ ; https://github.com/microsoft/api-guidelines | AIP status and "updated" date |
| HTTP standards | https://www.rfc-editor.org/rfc/rfc9457 ; IETF Datatracker (httpapi WG) | RFC vs Internet-Draft. Drafts expire |
| Telemetry maturity | https://opentelemetry.io/docs/specs/semconv/ ; https://opentelemetry.io/docs/concepts/signals/ | Stability badges per signal and language |
| Resilience and tenancy guidance | https://aws.amazon.com/builders-library/ ; https://docs.aws.amazon.com/wellarchitected/latest/framework/reliability.html ; https://learn.microsoft.com/en-us/azure/architecture/patterns/ | "Last modified" dates. Builders' Library URLs now redirect |
| Serverless and workflow limits and pricing | Vendor quota and pricing pages | Model cost at expected events per second |
| Application security requirements | https://owasp.org/www-project-application-security-verification-standard/ ; https://owasp.org/API-Security/ | Current ASVS version and API Security Top 10 edition; map the applicable requirements |
| Database feature semantics | https://www.postgresql.org/docs/current/ (or your DB's docs) | Docs for the major version you run |
| New book editions | Publisher or author pages, e.g. https://martin.kleppmann.com/2026/03/24/designing-data-intensive-applications-2e.html | Prefer the latest edition for data systems |
