---
name: architecture-decision-making
description: Use when choosing, changing or reviewing an architecture or technology. Frame, compare, decide, record.
metadata:
  version: "0.1.0"
  status: draft
  owner: maintainer
---

# Architecture decision-making

An end-to-end method for architecture and technology decisions: frame it, find the decisions nobody has
made, turn qualities into testable drivers, compare real alternatives, pick the simplest option that meets
every requirement, plan its evolution, and record why. Everything in architecture is a trade-off; if an
option looks free, its cost has not been found yet ([Richards & Ford](references/canon-reading-list.md)).

**Companion skills** (use them, don't repeat them): `due-diligence-before-deciding` (the always-on floor for
every choice; this skill is how an architect meets it), `architecture-research-and-verification` (evidence,
self-checks and the shared confidence rubric), `architecture-proposal-writing` (presenting the result), `use-existing-solutions-first`
(sourcing order), `work-planning` (plans and approval), `engineering-principles` EP-06 (simplicity at code level).
Domain decision guides live in `frontend-architecture`, `backend-and-data-architecture` and
`cloud-and-infrastructure-architecture`: starting candidates with when-not cases, to compare and verify against
the situation, never answers to adopt unchecked.

## When to use

- Choosing a style, platform, datastore, framework, hosting model, integration pattern, or build/buy/rent.
- Changing a hard-to-reverse decision, reviewing a design, or answering "what should we use for X?".

Skip the full method when constraints or a platform standard force the choice (say so, cite it), or for a
cheap two-way door: decide, note it, move on. Even then, name the alternatives you weighed and confirm the
chosen option works here; the depth shrinks, the floor does not.

## The values this protects

| Value | Means | Violation looks like |
|---|---|---|
| **Fit** | Meets today's real requirements, measured | "Scalable" with no number; a requirement nobody stated |
| **Simplicity** | Least machinery that meets all requirements | Components no named requirement needs |
| **Evolvability** | Cheap to change when needs change | Today's guess baked into data formats or public APIs |
| **Cost** | Total cost, including people's time to run it | A licence comparison that ignores on-call and upgrades |
| **Traceability** | The why survives the author | Rationale in someone's head; rejected options forgotten |

When two values conflict, name the conflict and say which value wins in this case and why.

## The method

Scale every step to the stakes. Sizing comes first.

### 1. Frame the decision

Write down, in a few lines:
- **Outcome.** What will be true when this is decided, in the requester's words.
- **Decision owner and stakeholders.** Who accepts it; who lives with it (operators, users, other teams).
- **Constraints.** Budget, deadline, skills, compliance, existing systems, the owner's standing preferences.
- **Horizon and phase.** How long it must last; is the product exploring, scaling or optimising
  ([Beck 3X](references/decision-heuristics.md#h15))?
- **Reversibility.** One-way door (data model of record, public API, security boundary, vendor contract) or
  two-way? Estimate the real switching cost: migration, retraining and contracts often make "reversible in
  theory" one-way in practice.

Pick the rigour tier:

| Tier | Use when | Depth |
|---|---|---|
| **Light** | Two-way door, small blast radius | Options in a sentence each, the chosen one checked to work here, a decision, a one-line note |
| **Standard** | Costly to reverse, or several teams or components affected | Full method, MADR record, lightweight peer review |
| **Heavy** | One-way door with high consequence (money, safety, privacy, core data) | Full method, explicit scenarios, verification spike, premortem, wider review |

Rigour follows risk ([Fairbanks](references/decision-heuristics.md#h11)).

### 2. Find the missing decisions first

In preliminary analysis of 18 non-random ATAM reports, 57 of 99 risk themes concerned a missing decision or
investigation ([SEI risk themes](references/review-question-bank.md)). Treat this as a prompt to search for
omissions, not a population estimate. Before comparing anything, list the decisions and investigations this
change implies: data ownership, identity, failure handling, backup and restore, observability, cost
controls, who operates it, what it replaces. Include "ban" decisions (things deliberately excluded). Note each
one's last responsible moment: the point after which waiting removes an important option.

### 3. Turn qualities into measurable drivers

- Write each important quality as a scenario: source, stimulus, environment, artifact, response, response
  measure. "Fast" is not a requirement. "p95 under 300 ms for search at 50 requests/s on normal load" is
  ([format and examples](references/quality-attributes-and-scenarios.md)).
- Rank the scenarios in a small utility tree: importance to the business × difficulty to achieve. The
  top three to five drive the decision.
- Sweep a quality checklist (ISO/IEC 25010, plus cost and operability): which did we ignore deliberately?
- Write **non-goals**: plausible goals that are deliberately out.
- When numbers can't be known yet, record the assumption and a revisit trigger. Don't invent precision.

### 4. Generate options before favouring one

Anchoring on the first idea is a well-documented bias in architecture decisions; one small experiment found
its practitioners more prone than its students ([debiasing study](references/decision-heuristics.md#sources)).
Write the option list before evaluating any option. Search by the problem, not only by names you already
know, and keep going until new searches stop turning up serious candidates. Always include:
1. **Keep current / do nothing.** What happens if we don't decide now?
2. **Extend what exists.** A capability of something already run (current database or platform).
3. **Buy, rent, self-host or build.** Sourcing order per `use-existing-solutions-first`.
4. **The simplest thing that could work** (the step 6 baseline).
5. **At least one unconventional option.** A different decomposition, removing the need, a manual
   process, or the "big" option at its honest cost.

If constraints leave only one viable option, say so and show why. Don't invent straw men.

### 5. Evaluate trade-offs

- **Verify each finalist here first.** Confirm from primary, current sources (official docs, live config,
  versions, real prices, compatibility lists, the code) that it works in this system, and run any cheap, safe
  check (read the config, a dry run, a small spike). "Should work" is a guess until checked.
- **Score each option against the ranked scenarios** qualitatively (✓ / ~ / ✗ with a reason), each criterion
  independently. No weighted decimal totals ([why](references/decision-heuristics.md#h17)).
- **Find sensitivity and trade-off points**, e.g. replication factor: availability vs write latency.
- **Total cost.** Build cost, run cost, *operator hours* (patching, upgrades, backups, on-call), team size and
  skills, and cost of delay. For a small team, toil often outweighs the bill.
- **Lock-in is a price, not a sin.** Treat switching cost as an option's strike price. Avoiding lock-in
  often adds complexity, which is its own lock-in ([Hohpe](references/decision-heuristics.md#h18)).
- **Innovation tokens.** Each new technology to learn and operate spends one; spend only where a requirement
  needs it.
- **Team fit (Conway).** Does the structure of the design match who builds and runs it?
- **Outside view and steelmen.** Check base rates; write each rejected option's strongest case
  (`architecture-research-and-verification`).

### 6. Run the complexity budget

Pick the baseline that fits the starting point:
- **Greenfield:** the smallest viable shape: one deployable (a modular monolith), one datastore, one region,
  one box or managed platform, tools the team already operates.
- **Existing system:** the capabilities it already runs and supports. Consolidating a distributed estate is
  a change with its own cost, not a free return to simplicity.

For **every** element a candidate adds (a service, store, queue, cluster, cache, second cloud, new language)
or removes, write the named requirement that earns it plus evidence (napkin maths, a measurement, a
constraint), and charge it the migration, compatibility and decommissioning cost.

- No named requirement, or only "we might need it" → cut it, or move it to the evolution path with a trigger.
- Exception: things that are cheap now and very expensive later (tenant isolation in the data model,
  auditability, data formats, security boundaries) can earn their place on cost-of-change grounds. Say so
  explicitly. YAGNI applies to speculative capabilities, never to what keeps the system easy to change
  (tests, modularity, deployability).

### 7. Design for today, with an explicit evolution path

- Size from the step 3 scenarios: forecast horizon, peak shape (bursts, seasonality, launch events) and the
  required response. Persistent data and external contracts deserve longer horizons.
- Sacrificial architecture is one pattern, not a rule: one team sized for about 10× and expected a rewrite
  before 100× ([H6](references/decision-heuristics.md#h6)). Don't use that ratio when contractual capacity,
  safety, a hard launch event or fat-tailed growth sets the load, or when the workload is small and bounded.
- Keep internal modularity even in a sacrificial design, so replacing it later is graceful.
- Buy options where uncertainty is high (a seam, a port, a portable data format). Skip them where it is low.
- Write the path as **triggers → next step**, e.g. "if job volume exceeds what the database queue
  handles under the p95 target, move jobs to a dedicated queue behind the existing interface."

### 8. Decide, with confidence and conditions

State the recommendation with a one-line reason; confidence as low / medium / high, given separately for
the facts, the forecasts and the recommendation, using the rubric in `architecture-research-and-verification`
(a number only with an explicit reference class); **what would change the decision**; what it does not
solve; and the fallback.
The agent proposes. The decision owner decides.

### 9. Record it

- Follow the repository's existing decision-record convention (location, numbering, template). If none
  exists, use MADR with a one-line Y-statement summary (templates in `architecture-proposal-writing`).
- Log architecturally significant decisions. A useful test: write a record if at least two hold: it spans
  components, it constrains future work, real alternatives existed, it is costly to reverse. Always record
  one that is high-consequence, externally visible, compliance-significant or expensive to reverse, even
  if it touches one component.
- Name the rejected options and the accepted downsides. A record with no downsides is advocacy.
- Status is **Proposed** until the decision owner accepts it. Supersede old records rather than editing them.

### 10. Confirm it over time

Turn each top driver into a **fitness function** where it can be measured objectively: a dependency rule in
CI, a latency budget in a load test, a cost alert, a restore test. Where a quality can't be measured honestly,
schedule a scenario review instead of faking a metric. Set a review date or trigger in the record.

## Reviewing a design

Use the [review question bank](references/review-question-bank.md): missing decisions first, then
unmeasured qualities, missing alternatives, unpriced operating costs and fallacies. Routine work needs a
couple of hours of peer evaluation; keep full ATAM-style evaluation for high-stakes systems.

## Pitfalls

- **Best-practice lookup** ("big companies use X"): their requirements aren't yours.
- **Résumé- or novelty-driven choices.** If hiring or learning matters, make it a driver.
- **Deferring a one-way door** in the name of the last responsible moment.
- **Analysis paralysis on a two-way door.** Time-box, decide, set a revisit trigger. The time box limits
  depth, never the floor: alternatives considered and the chosen option checked to work.

## Done when

- [ ] Tier chosen; reversibility and decision owner named.
- [ ] Missing and "ban" decisions listed, each with a last responsible moment.
- [ ] Top drivers written as measurable scenarios; non-goals listed.
- [ ] Options searched until no new serious candidates appeared, including keep-current, extend-existing and
  the simplest, each steelmanned, unless a hard constraint
  leaves fewer (constraint documented, no straw men).
- [ ] Complexity budget: every addition or removal names its requirement and migration cost.
- [ ] Load sized from forecast and peak scenarios, not a fixed multiplier.
- [ ] Cost includes operating effort; lock-in priced; new technologies counted.
- [ ] Evolution path written as triggers; confidence (facts, forecasts, recommendation) and flip conditions stated.
- [ ] Proposed record drafted in the repository's convention; fitness functions or a review date named.
- [ ] Each finalist, and the chosen option above all, checked to work in this situation against primary,
  current sources; no deciding fact rests on a caveat a check could settle; the receiver could act without
  asking for more homework.
- [ ] Evidence and self-checks done per `architecture-research-and-verification`.

## References

- [references/quality-attributes-and-scenarios.md](references/quality-attributes-and-scenarios.md): scenario format, utility tree, quality checklist, fitness functions.
- [references/decision-heuristics.md](references/decision-heuristics.md): rules of thumb with value, when-not and source; open contradictions.
- [references/review-question-bank.md](references/review-question-bank.md): questions for interrogating a design.
- [references/canon-reading-list.md](references/canon-reading-list.md): tiered reading list with year, type and link.
