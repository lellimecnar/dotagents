# Verification ladder

Climb only as high as the risk requires. Each rung costs more and retires a different kind of doubt. "Think
slow, act fast": do the cheap iteration (planning, modelling, simulation) before the expensive, irreversible step.
https://sbi.sydney.edu.au/think-slow-to-ensure-your-project-succeeds

| Rung | Cost | Retires | Use for tier |
|---|---|---|---|
| 0. Primary-source check | minutes | "Does it exist / do what we think?" | All |
| 1. Napkin maths | minutes–hour | "Is this plausible at our scale?" | All with a scale or cost question |
| 2. Spike | hours–days | One named unknown | Standard, Heavy |
| 3. Active benchmark | days | Performance and limiter claims | When performance decides |
| 4. Prototype / load test | days–weeks | End-to-end behaviour against scenarios | Heavy |
| 5. Formal model | weeks | Correctness of new concurrent or distributed protocols | Heavy, protocol design only |
| 6. Fitness functions / chaos | ongoing | Drift and resilience after delivery | Standard, Heavy |

## 0. Primary-source check
Verify features, limits, versions and maintenance status against official docs, registries and changelogs.
Record the URL and the date checked. See source-grading.md.

## 1. Napkin maths
- **Procedure (Eskildsen):** define the problem, the performance goals and the features. Estimate from first
  principles using order-of-magnitude numbers. Compare with reality where you can.
  https://sirupsen.com/napkin/process ; numbers and techniques https://github.com/sirupsen/napkin-math
- Anchors for orders of magnitude only: https://gist.github.com/jboner/2841832 ; https://brenocon.com/dean_perf.html .
  Re-measure on current hardware.
- **Decision rule:** if the design needs far more than the hardware or service can deliver even on paper, stop and
  rethink. If it needs a tiny fraction, the "scale" argument for complexity is probably not real.
- **Not for:** SLO commitments. Napkin numbers are orders of magnitude.

*Example (illustration):* 10,000 daily users × 20 requests each = 200,000 requests/day ≈ 2–3 requests/s on
average, perhaps 10–30/s at peak. That is well within what one modest server and one relational database
typically handle. Confirm against the actual stack before relying on it. A cluster is not earned by this load.

## 2. Spike
- A spike is "a very simple program to explore potential solutions", built for the one problem under examination;
  "expect to throw it away". http://www.extremeprogramming.org/rules/spike.html
- **Exit criteria, written first:** the question; the pass/fail threshold; the time box; what you'll do on pass
  and on fail.
- **Not when:** the "spike" is going to production. Then it's an MVP and needs production engineering.

## 3. Active benchmark
- "You benchmark A, but actually measure B, and conclude you've measured C." Run long, steady-state benchmarks, and
  analyse the real limiter (CPU, I/O, lock, network, the load generator) *while* the benchmark runs.
  https://www.brendangregg.com/activebenchmarking.html
- **Gregg's checklist:** Why not double? Was it tuned? Did it break limits? Did it error? Does it reproduce? Does it
  matter? Did it even happen? https://www.brendangregg.com/blog/2018-06-30/benchmarking-checklist.html
- **Fairness:** same tuning effort, same cache state, same flags, a workload that matches yours. Even peer-reviewed
  benchmarks commit "benchmarking crimes" (https://arxiv.org/abs/1801.02381). DBMS comparisons are especially
  fragile (https://dl.acm.org/doi/10.1145/3209950.3209955).
- **Accept a result only if** you can name the limiter and it reproduces. Fix the configuration *before* looking at
  results, to avoid the garden of forking paths. https://stat.columbia.edu/~gelman/research/unpublished/forking.pdf

## 4. Prototype and load test
- Drive the top scenarios from the utility tree with realistic data volume and traffic shape, including the failure
  scenarios (dependency down, zone lost).
- Pass/fail is the scenario's response measure, set before the test.
- Capacity inputs to forecast: users, request rate, history, seasonality, events, growth.
  https://cloud.google.com/architecture/infra-reliability-guide/traffic-load

## 5. Formal model
- For novel concurrency, replication, consensus or failure-recovery protocols. AWS used TLA+ on "10 large
  complex real-world systems": "In every case TLA+ has added significant value". In DynamoDB's replication
  work, "the shortest error trace exhibiting the bug contained 35 high level steps", and the bug "had passed
  unnoticed through extensive design reviews, code reviews, and testing". Engineers "have been able to learn TLA+
  from scratch and get useful results in 2 to 3 weeks". This is one 2014 experience report from one company,
  not a controlled comparison. Quotes re-verified against the PDF text on 2026-11-19.
  https://lamport.azurewebsites.net/tla/formal-methods-amazon.pdf
- A model verifies only what you specify. AWS reports failing to find a liveness bug "as we did not check liveness".
- **Not when:** CRUD services or well-trodden managed components. The cost outweighs the benefit.

## 6. Fitness functions and chaos
- Encode key qualities as automated checks in the pipeline (dependency rules, latency budgets, coupling).
  https://evolutionaryarchitecture.com/
- Treat resilience claims as falsifiable: define a measurable steady state, hypothesise it holds, inject real-world
  faults, try to disprove it, and keep the blast radius small. http://principlesofchaos.org/
- **Not when:** a quality can't be measured cheaply or meaningfully. Don't create metrics that become targets.

## Reporting what you verified

For each material claim, state which rung it reached: "checked in official docs (date)", "napkin estimate",
"spike passed: <criterion>", "benchmarked: limiter = disk I/O, reproduced 3×", or, for a non-deciding claim
only, "**not verified**: <what was tried>, <why no means could settle it>, risk if wrong: <impact>".
