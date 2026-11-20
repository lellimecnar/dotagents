# Reading list

Tiered by when to read. Tier 1 is the minimum for anyone using this skill. Tier 2 deepens a
specific decision step. Tier 3 is case studies and reference material to consult when relevant.
URLs checked 2026-11-19 with a scripted browser request (one retry): all resolve. † marks publisher pages that refuse scripted clients (HTTP 403); each was confirmed live by a recent web-archive capture returning 200.

## Tier 1: read first

| Title | Author | Year | Type | URL | Why |
|---|---|---|---|---|---|
| Designing Data-Intensive Applications (1e; 2e published March 2026) | M. Kleppmann (2e with C. Riccomini) | 2017 / 2026 | Textbook | https://dataintensive.net/ ; https://martin.kleppmann.com/2026/03/24/designing-data-intensive-applications-2e.html | The reference for replication, partitioning, transactions, consistency, streams |
| MonolithFirst; Microservice Premium; Microservice Trade-Offs | M. Fowler | 2015 | Blog | https://martinfowler.com/bliki/MonolithFirst.html ; https://martinfowler.com/bliki/MicroservicePremium.html ; https://martinfowler.com/articles/microservice-trade-offs.html | The default and the cost ledger |
| What do you mean by "Event-Driven"? | M. Fowler | 2017 | Article | https://martinfowler.com/articles/201701-event-driven.html | Four different meanings of one phrase |
| Choose Boring Technology | D. McKinley | 2015 | Essay | https://mcfunley.com/choose-boring-technology | Innovation tokens for technology choices |
| Please stop calling databases CP or AP | M. Kleppmann | 2015 | Blog | https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html | Precise consistency vocabulary |
| Timeouts, retries and backoff with jitter | M. Brooker (Amazon Builders' Library) | 2019 | Practitioner article | https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/ | Resilience defaults |
| Making retries safe with idempotent APIs | Amazon Builders' Library | — | Practitioner article | https://aws.amazon.com/builders-library/making-retries-safe-with-idempotent-apis/ | Idempotency by contract |
| Hyrum's Law | H. Wright | n.d. | Essay | https://www.hyrumslaw.com/ | Every observable behaviour becomes contract |
| Modular Monolith: A Primer | K. Grzybek | 2019 | Blog | https://www.kamilgrzybek.com/blog/posts/modular-monolith-primer | What the default looks like |

## Tier 2: deepen a decision step

| Title | Author | Year | Type | URL | Step |
|---|---|---|---|---|---|
| DDD Reference | E. Evans | 2015 | Reference | https://www.domainlanguage.com/ddd/reference/ | Domain |
| Learning Domain-Driven Design | V. Khononov | 2021 | Book | https://oreilly.com/library/view/learning-domain-driven-design/9781098100124/part03.html † | Domain: subdomain types |
| Balancing Coupling in Software Design | V. Khononov | 2024 | Book / site | https://coupling.dev/ | Modules: strength, distance, volatility |
| On the criteria to be used in decomposing systems into modules | D. Parnas | 1972 | Paper | https://dl.acm.org/doi/10.1145/361598.361623 † | Modules: information hiding |
| Building Microservices, 2nd ed. | S. Newman | 2021 | Book | https://samnewman.io/books/building_microservices_2nd_edition/ | Topology, including when not to |
| Monolith to Microservices | S. Newman | 2019 | Book | https://samnewman.io/books/monolith-to-microservices/ | Topology: decomposition and data splitting |
| Serverless Computing: One Step Forward, Two Steps Back | J. Hellerstein et al. | 2018 | Paper (CIDR'19) | https://arxiv.org/abs/1812.03651 | Topology: serverless limits |
| Google AIPs 121, 155, 180, 185 | Google | 2019–2024 | Guide | https://google.aip.dev/180 | API design and compatibility |
| Zalando RESTful API and Event Guidelines | Zalando | living | Guide | https://opensource.zalando.com/restful-api-guidelines/ | API house style |
| APIs as infrastructure: future-proofing Stripe with versioning | Stripe Engineering | 2017 | Blog | https://stripe.com/blog/api-versioning | API evolution |
| CQRS | M. Fowler | 2011 | Blog | https://martinfowler.com/bliki/CQRS.html | Consistency: when not to |
| Sagas | H. Garcia-Molina, K. Salem | 1987 | Paper | https://www.cs.cornell.edu/andru/cs711/2002fa/reading/sagas.pdf | Consistency: compensations |
| Life beyond Distributed Transactions | P. Helland | 2016 (2007) | Paper | https://queue.acm.org/detail.cfm?id=3025012 † | Consistency: entities and messaging |
| microservices.io data patterns | C. Richardson | living | Pattern catalogue | https://microservices.io/patterns/data/saga.html | Saga, outbox, database per service |
| The Log | J. Kreps | 2013 | Article | https://web.archive.org/web/20180201201617/https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying | Integration: queue vs log |
| CAP Twelve Years Later | E. Brewer | 2012 | Article | https://www.infoq.com/articles/cap-twelve-years-later-how-the-rules-have-changed/ | Consistency |
| Consistency Tradeoffs in Modern Distributed Database System Design (PACELC) | D. Abadi | 2012 | Paper | https://www.cs.umd.edu/~abadi/papers/abadi-pacelc.pdf | Consistency |
| Jepsen analyses and consistency map | K. Kingsbury | ongoing | Test reports | https://jepsen.io/analyses | Store selection |
| Release It!, 2nd ed. | M. Nygard | 2018 | Book | https://pragprog.com/titles/mnee2/release-it-second-edition/ | Resilience patterns |
| Google SRE book: Handling Overload; Addressing Cascading Failures | Google | 2016 | Book chapters | https://sre.google/sre-book/handling-overload/ ; https://sre.google/sre-book/addressing-cascading-failures/ | Resilience |
| Metastable Failures in Distributed Systems | N. Bronson et al. | 2021 | Paper (HotOS) | https://sigops.org/s/conferences/hotos/2021/papers/hotos21-s11-bronson.pdf | Resilience |
| The Tail at Scale | J. Dean, L. Barroso | 2013 | Paper (CACM) | https://research.google/pubs/the-tail-at-scale/ | Resilience: fan-out latency |
| Exponential Backoff and Jitter | M. Brooker | 2015 | Blog | https://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/ | Resilience |
| AWS SaaS Lens: silo, pool and bridge models | AWS | living | Guide | https://docs.aws.amazon.com/wellarchitected/latest/saas-lens/silo-pool-and-bridge-models.html | Multi-tenancy |
| Azure multitenant storage and data approaches | Microsoft | living | Guide | https://learn.microsoft.com/en-us/azure/architecture/guide/multitenant/approaches/storage-data | Multi-tenancy |
| OpenTelemetry signals and semantic conventions | CNCF OpenTelemetry | living | Spec | https://opentelemetry.io/docs/concepts/signals/ | Observability |
| There is only one key difference between observability 1.0 and 2.0 | C. Majors | 2024 | Blog | https://charity.wtf/p/there-is-only-one-key-difference-between-observability-1-0-and-2-0 | Observability: wide events |
| Appropriate Uses for SQLite | SQLite | living | Docs | https://www.sqlite.org/whentouse.html | Store selection |
| pgvector README | pgvector | living | Docs | https://github.com/pgvector/pgvector | AI-era data: exact vs approximate (recall against exhaustive k-NN, not semantic relevance) |
| Retrieval-Augmented Generation for LLMs: A Survey | Y. Gao et al. | 2023 | Paper | https://arxiv.org/abs/2312.10997 | AI-era data |
| Introducing Contextual Retrieval | Anthropic | 2024 | Engineering blog | https://www.anthropic.com/news/contextual-retrieval | AI-era data: dated vendor result on its own evaluation; candidate levers to test on your evaluation set |

## Tier 3: case studies and reference

| Title | Author | Year | Type | URL | Why |
|---|---|---|---|---|---|
| Deconstructing the Monolith; Under Deconstruction; Packwerk | Shopify Engineering | 2019–2020 | Case study | https://shopify.engineering/deconstructing-monolith-designing-software-maximizes-developer-productivity ; https://shopify.engineering/shopify-monolith ; https://shopify.engineering/enforcing-modularity-rails-apps-packwerk | Modular monolith at scale; the dependency-graph lesson |
| Goodbye Microservices | A. Noonan (Segment) | 2018 | Case study | https://www.twilio.com/en-us/blog/developers/best-practices/goodbye-microservices | Consolidating 140+ services |
| Scaling up the Prime Video audio/video monitoring service (archived) | M. Kolny | 2023 | Case study | https://web.archive.org/web/20240325042615/https://www.primevideotech.com/video-streaming/scaling-up-the-prime-video-audio-video-monitoring-service-and-reducing-costs-by-90 | Cost shape of serverless orchestration. Original page removed |
| Introducing Domain-Oriented Microservice Architecture | Uber Engineering | 2020 | Case study | https://www.uber.com/us/en/blog/microservice-architecture/ | Regrouping thousands of services |
| The Majestic Monolith | DHH | 2016 | Blog | https://signalvnoise.com/svn3/the-majestic-monolith/ | Practitioner case for an integrated monolith |
| Don't start with a monolith | S. Tilkov | 2015 | Article | https://martinfowler.com/articles/dont-start-monolith.html | The counter-view to monolith-first |
| Microservices | J. Lewis, M. Fowler | 2014 | Article | https://martinfowler.com/articles/microservices.html | Original definition |
| Towards Modern Development of Cloud Applications | S. Ghemawat et al. | 2023 | Paper (HotOS) | https://sigops.org/s/conferences/hotos/2023/papers/ghemawat.pdf | Logical vs physical boundaries |
| Implementing Domain-Driven Design | V. Vernon | 2013 | Book | https://www.informit.com/store/implementing-domain-driven-design-9780321834577 | Aggregates and context mapping in practice |
| Context Mapping | DDD-Crew | n.d. | Toolkit | https://github.com/ddd-crew/context-mapping | Relationship patterns |
| Connascence | community | n.d. | Reference | https://connascence.io/ | Coupling vocabulary |
| Microsoft REST API Guidelines | Microsoft | living | Guide | https://github.com/microsoft/api-guidelines | Alternative house style |
| Architectural Styles (ch. 5) | R. Fielding | 2000 | Thesis | https://ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm | What REST actually constrains |
| Consumer-Driven Contracts | I. Robinson | 2006 | Article | https://martinfowler.com/articles/consumerDrivenContracts.html | Evolution through consumer expectations |
| Enterprise Integration Patterns | G. Hohpe, B. Woolf | 2003 | Book / site | https://www.enterpriseintegrationpatterns.com/ | Messaging vocabulary |
| Turning the database inside-out | M. Kleppmann | 2015 | Talk / blog | https://martin.kleppmann.com/2015/03/04/turning-the-database-inside-out.html | Streams and derived views |
| Immutability Changes Everything | P. Helland | 2015 | Paper (CIDR) | https://cidrdb.org/cidr2015/Papers/CIDR15_Paper16.pdf | Append-only thinking |
| Hermitage | M. Kleppmann | 2014– | Test suite | https://github.com/ept/hermitage | Real isolation-level behaviour |
| Highly Available Transactions | P. Bailis et al. | 2014 | Paper (VLDB) | https://www.bailis.org/papers/hat-vldb2014.pdf | Guarantees that survive partitions |
| Dynamo | G. DeCandia et al. | 2007 | Paper (SOSP) | https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf | Leaderless replication |
| Time, Clocks, and the Ordering of Events | L. Lamport | 1978 | Paper | https://lamport.azurewebsites.net/pubs/time-clocks.pdf | Causality |
| Patterns of Distributed Systems | U. Joshi | 2020–2023 | Catalogue | https://martinfowler.com/articles/patterns-of-distributed-systems/ | WAL, quorum, leases |
| Scaling Memcache at Facebook | R. Nishtala et al. | 2013 | Paper (NSDI) | https://www.usenix.org/conference/nsdi13/technical-sessions/presentation/nishtala | Caching at scale |
| Amazon Builders' Library (index) | Amazon engineers | 2019– | Practitioner articles | https://aws.amazon.com/builders-library/ | Load shedding, caching, shuffle sharding, fallback, queues |
| Marc Brooker on retries and circuit breakers | M. Brooker | 2022 | Blog | https://brooker.co.za/blog/2022/02/28/retries.html | Modelling retry behaviour |
| SRE Workbook: Managing Load | Google | 2018 | Book chapter | https://sre.google/workbook/managing-load | Load balancing, autoscaling, shedding |
| Just Use Postgres for Everything | S. Schmidt | 2025 rev. | Blog (advocacy) | https://www.amazingcto.com/postgres-for-everything/ | Postgres-first feature mapping; check against your workload |
| I'm All-In on Server-Side SQLite | B. Johnson (Fly.io) | 2022 | Blog | https://fly.io/blog/all-in-on-sqlite-litestream/ | SQLite with replication in production |
| A Berkeley View on Serverless Computing | E. Jonas et al. | 2019 | Paper | https://arxiv.org/abs/1902.03383 | The serverless promise |
| What is Temporal; What is Durable Execution | Temporal | 2025 | Docs / blog | https://docs.temporal.io/temporal ; https://temporal.io/blog/what-is-durable-execution | Workflow-engine category |
| Retrieval-Augmented Generation for Knowledge-Intensive NLP | P. Lewis et al. | 2020 | Paper | https://arxiv.org/abs/2005.11401 | RAG origin |
| Building Effective Agents | Anthropic | 2024 | Engineering blog | https://www.anthropic.com/engineering/building-effective-agents | Workflows before agents |
| Azure Cloud Design Patterns | Microsoft | living | Catalogue | https://learn.microsoft.com/en-us/azure/architecture/patterns/ | Each pattern with "when not to use" |
| AWS Prescriptive Guidance: cloud design patterns | AWS | living | Catalogue | https://docs.aws.amazon.com/prescriptive-guidance/latest/cloud-design-patterns/introduction.html | Same patterns, AWS implementations |
| When to use microservices (and when not to) | S. Newman, M. Fowler | 2020 | Talk | https://www.youtube.com/watch?v=GBTdnfD6s5Q | The decision method in video form |
