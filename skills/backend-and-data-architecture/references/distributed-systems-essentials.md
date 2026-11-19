# Distributed-systems essentials

The background an architect needs to state consistency requirements precisely, pick isolation
levels deliberately, and anticipate how distributed systems fail. Durable concepts only. All URLs
come from the research dossiers (compiled 2026-11-19). † marks canonical URLs that returned 403 to
a scripted client.

## 1. Consistency models: say exactly what you need

- **Drop the CAP slogan.** Brewer, CAP's author: "The '2 of 3' formulation was always misleading"
  (2012) https://www.infoq.com/articles/cap-twelve-years-later-how-the-rules-have-changed/.
  The C in CAP means linearizability and is unrelated to the C in ACID, so labelling databases "CP"
  or "AP" hides more than it says (Kleppmann, 2015)
  https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html.
- **Use the hierarchy of named models.** Jepsen's consistency map relates linearizable,
  sequential, causal, read-your-writes, serializable, snapshot isolation and weaker models, and
  says which are available under partition https://jepsen.io/consistency.
- **Use PACELC for the everyday trade-off.** If there is a Partition, choose Availability or
  Consistency. Else, choose Latency or Consistency. "Systems that sacrifice consistency … tend to do
  so all the time, not just when there is a network partition" (Abadi, 2012)
  https://www.cs.umd.edu/~abadi/papers/abadi-pacelc.pdf ;
  https://dbmsmusings.blogspot.com/2010/04/problems-with-cap-and-yahoos-little.html.
- **Which guarantees can stay highly available.** Bailis et al., Highly Available Transactions
  (VLDB 2014) https://www.bailis.org/papers/hat-vldb2014.pdf.
- **How to write the requirement.** Per flow: the model (e.g. "read-your-writes for the author,
  eventual within N seconds for others"), the staleness bound, and the behaviour during a
  partition. Then check the chosen store against that statement, not against its label.

## 2. Transactions and isolation

- **Isolation names do not mean the same thing across databases.** Hermitage tests which
  anomalies (dirty write, lost update, write skew, and so on) each database permits at each named
  level https://github.com/ept/hermitage. Read your database's own isolation docs for the
  version you run, e.g. https://www.postgresql.org/docs/current/transaction-iso.html.
- **Claims can be wrong even in mature systems.** Jepsen found PostgreSQL 12.3's SERIALIZABLE
  transactions "were not, in fact, serializable" (G2-item anomaly; fixed afterwards, 2020)
  https://jepsen.io/analyses/postgresql-12.3. The lesson is to verify, not to distrust one product.
- **Avoid distributed two-phase commit across services when autonomy matters.** 2PC blocks
  participants while the coordinator is unavailable, couples their deployments, and needs every
  store to support the same protocol. Where autonomous deployment, heterogeneous stores or
  partition tolerance matter, use entities plus messaging (Helland)
  https://queue.acm.org/detail.cfm?id=3025012 †, sagas with compensations (Garcia-Molina &
  Salem, 1987) https://www.cs.cornell.edu/andru/cs711/2002fa/reading/sagas.pdf, and a
  transactional outbox https://microservices.io/patterns/data/transactional-outbox.html.
  *When 2PC can be viable:* one coordinator, a small bounded set of participants that support it
  (for example two databases under one team), and blocking during coordinator failure that the
  business has accepted. Document those conditions in the decision record.
- **Delivery semantics.** Networks give at-most-once or at-least-once. "Exactly-once" in a broker
  combines an idempotent producer with transactions and holds within that broker only
  https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it/.
  End to end, you get effectively-once only with idempotent consumers and caller-supplied request
  IDs https://aws.amazon.com/builders-library/making-retries-safe-with-idempotent-apis/.
- **Think append-only.** Immutable, append-only data is the basis of logs, replication and
  derived views (Helland, "Immutability Changes Everything", CIDR 2015) https://cidrdb.org/cidr2015/Papers/CIDR15_Paper16.pdf. A log can be the
  source of truth with derived views (Kleppmann, 2015)
  https://martin.kleppmann.com/2015/03/04/turning-the-database-inside-out.html.

## 3. Time, order and agreement

- Physical clocks drift. Causality needs logical clocks (Lamport, 1978)
  https://lamport.azurewebsites.net/pubs/time-clocks.pdf.
- Leaderless replication, quorums and conflict handling: Dynamo (SOSP 2007)
  https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf.
- Consensus for leader election and replicated logs: Raft https://raft.github.io/.
- Implementation patterns (write-ahead log, quorum, leases, high-water mark): Patterns of
  Distributed Systems https://martinfowler.com/articles/patterns-of-distributed-systems/.
- For concurrent, fault-tolerant protocols you design yourself, model-check them. AWS used TLA+ on
  10 large systems and found a DynamoDB bug with a 35-step trace that had passed design reviews,
  code reviews and testing (CACM 2015) https://lamport.azurewebsites.net/tla/formal-methods-amazon.pdf.

## 4. Failure modes to design against

| Failure mode | Mechanism | Counter | Sources |
|---|---|---|---|
| Retry amplification | Retries at every layer multiply load: four attempts per layer across three retrying layers can produce 4³ = 64 attempts; AWS's example uses three attempts across five layers, 3⁵ = 243 | Retry at one layer, budget retries, propagate deadlines | https://sre.google/sre-book/addressing-cascading-failures/ ; https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/ |
| Synchronised retries | Clients back off in lockstep | Capped exponential backoff with jitter | https://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/ |
| Metastable failure | Retries or cache loss keep the system overloaded after the trigger has gone | Load shedding, retry budgets, capacity for cold caches | https://sigops.org/s/conferences/hotos/2021/papers/hotos21-s11-bronson.pdf |
| Overload collapse | Goodput falls as queued work times out | Shed load early, client-side adaptive throttling | https://aws.amazon.com/builders-library/using-load-shedding-to-avoid-overload/ ; https://sre.google/sre-book/handling-overload/ |
| Insurmountable backlog | Unbounded queue grows faster than it drains | Bound queues, TTLs, LIFO under overload, separate queues | https://aws.amazon.com/builders-library/avoiding-insurmountable-queue-backlogs/ |
| Tail latency at fan-out | One slow leaf delays the whole request | Hedged or tied requests, fewer fan-out hops | https://research.google/pubs/the-tail-at-scale/ |
| Thundering herd, stale sets | Many misses hit the database at once | Leases, request coalescing, soft TTLs | https://www.usenix.org/conference/nsdi13/technical-sessions/presentation/nishtala ; https://aws.amazon.com/builders-library/caching-challenges-and-strategies/ |
| Latent fallback bugs | Rarely run code paths fail when finally needed | Strengthen the primary path; exercise degradation continuously | https://aws.amazon.com/builders-library/avoiding-fallback-in-distributed-systems/ |
| Correlated blast radius | One bad tenant or request takes down all | Cells and shuffle sharding (8 workers give 28 two-worker combinations) | https://aws.amazon.com/builders-library/workload-isolation-using-shuffle-sharding/ |
| Breaker-induced outage | A circuit breaker turns partial failure into full failure in sharded systems | Prefer retry token buckets. Use breakers with care | https://brooker.co.za/blog/2022/02/16/circuit-breakers/ ; https://brooker.co.za/blog/2022/02/28/retries.html |
| Remote-call fallacy | Treating remote calls like local ones; "remote calls are slow and are always at risk of failure" | Coarse-grained interfaces; don't distribute objects | https://martinfowler.com/articles/microservice-trade-offs.html ; https://martinfowler.com/articles/distributed-objects-microservices.html |

## 5. Napkin maths pointers

Estimate before you design or benchmark, and stop if the design needs far more headroom than the
hardware offers. Treat published latency tables as orders of magnitude only, and re-verify them.

- Method: define the problem, the performance goals and the features, reason from first principles,
  then check against reality https://sirupsen.com/napkin/process ; numbers and techniques
  https://github.com/sirupsen/napkin-math.
- Order-of-magnitude anchors (dated "~2012"; re-verify for current hardware)
  https://gist.github.com/jboner/2841832.
- Timeout choice from latency percentiles and an acceptable false-timeout rate
  https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/.
- Benchmarking discipline (what you actually measured, did it break limits, does it reproduce)
  https://www.brendangregg.com/blog/2018-06-30/benchmarking-checklist.html. Detailed rigour is
  owned by `architecture-research-and-verification`.

Questions to answer on the napkin for a back-end design: peak requests per second and its ratio to
average; bytes per request and total data growth per year; writes per second on the hottest key or
partition; fan-out per request and its effect on tail latency; working-set size versus memory; and
cost per million requests on the chosen platform.
