# Quality attributes and scenarios

How to turn "it should be fast, scalable and cheap" into drivers you can decide against and test later.

## 1. The six-part scenario

| Part | Question | Example (illustration) |
|---|---|---|
| Source of stimulus | Who or what triggers it? | Signed-in user |
| Stimulus | What happens? | Submits a search |
| Environment | Under what conditions? | Normal load, 50 req/s |
| Artifact | What part of the system? | Search API and index |
| Response | What should the system do? | Return ranked results |
| Response measure | How do we know it did? | p95 latency < 300 ms; error rate < 0.1% |

The response measure is what turns an adjective into a requirement. Write scenarios for the stressful cases
too: peak load, a dependency down, a region lost, a new developer making a change, an attacker.
Source: SEI quality-attribute scenarios (Software Architecture in Practice)
https://people.ece.ubc.ca/matei/EECE417/BASS/ch04lev1sec3.html

**Change scenarios count.** Maintainability and evolvability get scenarios too, e.g. "A developer adds a new
payment provider; artifact: payments module; response measure: done in ≤ 2 days with no change outside the
module." These drive modularity decisions.

## 2. Utility tree: rank before you weigh

Top-down: business goal → quality → refined attribute → concrete scenario, each scenario rated
(Importance, Difficulty) as H/M/L. The (H,H) scenarios drive the architecture. The (L,*) ones are checked
but rarely decisive. Analyse options at **sensitivity points** (a parameter a quality depends on) and
**trade-off points** (a parameter that is a sensitivity point for two or more qualities). Record risks,
non-risks, sensitivity points and trade-off points.
Source: ATAM, CMU/SEI-2000-TR-004 https://www.sei.cmu.edu/documents/629/2000_005_001_13706.pdf

```text
Utility
├── Performance
│   └── (H,M) Search p95 < 300 ms at 50 req/s, normal load
├── Availability
│   └── (H,H) Checkout survives loss of one zone; RTO 15 min, RPO 5 min
├── Modifiability
│   └── (M,L) New payment provider in ≤ 2 days, one module touched
└── Cost
    └── (H,M) Infra + ops ≤ <budget> per month at current load; ≤ 2× at forecast peak load
```
*(Illustration: numbers are placeholders to show the form.)*

## 3. Completeness checklist

Sweep the ISO/IEC 25010:2023 product-quality characteristics and ask "which did we ignore, and was that
deliberate?": functional suitability, performance efficiency, compatibility, interaction capability (formerly
usability), reliability, security, maintainability, flexibility (formerly portability; includes scalability),
safety. https://quality.arc42.org/standards/iso-25010 (check revision status at https://www.iso.org/standard/78176.html)

Add the ones product models underplay:
- **Cost**, as a requirement, per unit of business value. https://thefrugalarchitect.com/ ; https://www.finops.org/framework/principles/
- **Operability:** who runs it, toil, on-call. https://sre.google/sre-book/eliminating-toil/
- **Observability:** can we tell why it's slow or broken?
- **Compliance and privacy:** data protection by design and by default.
  https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-resources/accountability-and-governance/guide-to-accountability-and-governance/data-protection-by-design-and-by-default/
- **Sustainability**, where it matters: emissions per functional unit (SCI). https://sci.greensoftware.foundation/

Cloud well-architected frameworks are useful question banks for these, not sources of truth. Read their
trade-off sections. https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html ;
https://learn.microsoft.com/en-us/azure/well-architected/pillars ; https://cloud.google.com/architecture/framework

## 4. When numbers aren't knowable yet

- Agree a **landing zone** (an acceptable range the stakeholders sign up to) rather than one fake-precise number. This counters
  the "blind flight" fallacy of having no measurable NFRs. https://ozimmer.ch/practices/2025/09/01/ADMFallacies.html
- Otherwise record an **assumption** ("we assume < 1,000 daily users for 12 months") plus a **trigger** ("revisit
  if weekly actives exceed 500").
- Estimate on a napkin first; see `architecture-research-and-verification`.

## 5. Reliability targets

100% is almost never the right target. Set SLOs below it, keep an internal SLO tighter than any published one,
and get stakeholders to approve the targets.
https://sre.google/sre-book/embracing-risk/ ; https://sre.google/sre-book/service-level-objectives/ ;
https://sre.google/workbook/implementing-slos/

## 6. From scenario to fitness function

"An architectural fitness function is any mechanism that provides an objective integrity assessment of some
architectural characteristic(s)." https://www.infoq.com/articles/fitness-functions-architecture/

| Scenario kind | Fitness function (examples) |
|---|---|
| Module boundaries / dependency direction | Architecture rule tests in CI (ArchUnit-style), import-boundary lints |
| Latency / throughput budget | Load test in pipeline against the response measure |
| Availability / resilience | Chaos experiment with a stated steady-state hypothesis http://principlesofchaos.org/ |
| Recovery (RTO/RPO) | Scheduled restore test, timed |
| Cost | Budget alert; cost per unit tracked per release |
| Security baseline | Dependency and supply-chain checks, verification level per risk https://owasp.org/www-project-application-security-verification-standard/ |

If a quality cannot be measured objectively, use periodic scenario reviews, not an invented metric.
https://thoughtworks.com/insights/articles/fitness-function-driven-development ;
https://thoughtworks.com/en-us/insights/books/building-evolutionaryarchitectures-second-edition
