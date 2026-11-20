---
name: architecture-research-and-verification
description: Use when an architecture choice rests on facts or reasoning. Research, grade, verify, self-interrogate.
metadata:
  version: "0.1.0"
  status: draft
  owner: maintainer
---

# Architecture research and verification

How to find the evidence behind an architecture decision, judge its worth, check volatile facts against a
live source, and question your own reasoning first. Companions: `architecture-decision-making` (method),
`architecture-proposal-writing` (presentation), `use-existing-solutions-first` (sourcing order),
`due-diligence-before-deciding` (the always-on floor: no recommendation rests on a fact you could have checked).

## When to use

- A recommendation depends on facts: a feature exists, a limit, a cost, maturity, performance, a paper's finding.
- You feel sure. One small experiment (16 students, 20 practitioners) found its practitioner group more prone
  to anchoring ([Borowa et al. 2025](references/debiasing-toolkit.md#sources)): a reason to run the checks, not
  a population estimate. Language models sound equally sure when wrong.
- A requester has a preferred answer, or someone pushes back on yours.

Scale effort to the decision's tier. A two-way door needs a short look, not a literature review, but never less
than the floor: real alternatives considered and the chosen option's key facts checked.

## The values this protects

| Value | Means | Violation looks like |
|---|---|---|
| **Truth** | Claims match current reality | A package, flag or limit quoted from memory |
| **Calibration** | Confidence matches evidence | "Definitely" on a single blog post |
| **Independence** | Checks don't inherit the draft's bias | "I reviewed my answer and it's correct" |
| **Economy** | Evidence effort matches stakes | A week of research on a reversible choice |

## 1. Plan the research before searching

Write five lines first:
1. **Questions.** What must be true for each option to work? Turn each into a question.
2. **Decision relevance.** Which answer would change the recommendation? Drop questions that wouldn't.
3. **Claim class and where its answer lives** (matrix in step 2).
4. **Search terms, including the opposite case**: "X problems", "migrating off X", "X postmortem", "X vs Y".
5. **Stopping rule, by claim class and risk.** Stop when the evidence each deciding claim needs is in hand and
   new searches stop turning up options or evidence that could change the answer. A time box orders the work;
   it never ends it with a deciding claim still unchecked:
   - existence, syntax, limits: one versioned primary source for the version you'll use;
   - contentious operational claims: primary source plus independent corroboration;
   - effect or causal claims: a suitable review or replication; if none exists after searching, say the evidence
     is thin and do not let that claim carry the choice (go conditional or choose on facts you could verify).

   Sources are independent only if they don't share an origin or data set; ten posts quoting one benchmark
   are one source.

## 2. Match the source to the claim, then grade it

No source type is best for every claim. Official docs are authoritative for supported syntax but weak
independent evidence of comparative performance; a peer-reviewed paper on a mismatched workload loses to a
reproducible benchmark on yours ([matrix and rubric](references/source-grading.md)).

| Claim class | Fit-for-claim evidence |
|---|---|
| Current product behaviour (feature, limit, API, price) | Versioned official docs, source code, registry, or your own experiment |
| Comparative performance | Reproducible benchmark on the target workload, ideally your own |
| Causal or effect claim (bias, productivity, failure rate) | Suitable empirical research, judged against its method's empirical standard |
| Operating experience (toil, failure modes, migration pain) | Postmortems and experience reports, corroborated independently |

Then grade each source on orthogonal axes: authority, method, objectivity and date (from Garousi et al.'s
grey-literature criteria). Vendor material can still be fit for "this feature exists"; it is weak for "this
is faster". Record the **publication year** and the **date you checked** anything that can change.

## 3. Find and read papers efficiently

- Search scholarly indexes and preprint servers, then check whether a preprint has a peer-reviewed version.
- Read in three passes (Keshav): a 5–10 minute triage pass, a content pass, and a full re-derivation only for the
  paper the decision rests on.
- Pin the exact version: DOI or arXiv version, preprint or peer-reviewed, and publication status. Check for
  corrections, retractions and later replications or rebuttals. Save the version you used.
- Judge validity: research strategy, sample, preregistration, data and code availability, analysis plan,
  funding, and whether its context matches yours.
- Details: [references/finding-and-reading-papers.md](references/finding-and-reading-papers.md).

## 4. Verify every volatile fact at task time

Never accept a package name, API, flag, configuration key, version, limit, quota, price or maintenance status
from memory, yours or a model's. In a large study, language models invented package names in at least 5.2% of
samples for commercial models and 21.7% for open-source ones. A later replication on frontier models still
found 3.6–5.0% ([details](references/llm-failure-modes.md)).

Check each in the official docs or registry, note URL and date, and confirm it applies to your version. If one
route fails, try another (source code, registry, changelog, a spike). Only what no available means can settle may
stay unverified, listed with what you tried; it must not carry the recommendation.

## 5. Interrogate your own reasoning

Knowing about biases doesn't fix them. Practising specific techniques does ([toolkit](references/debiasing-toolkit.md)).
Run these as steps, not intentions:

- **Options before preference.** Write the option list before scoring or favouring any option. Include keep-current
  or configure-what-exists, the built-in or already-owned route, and the main alternatives found by searching on
  the problem.
- **Assumption register.** For the leading option: "what would have to be true?" List each assumption, how you'd
  test it, and its status (verified / plausible / unverified). Test every deciding assumption before you
  recommend; only non-deciding ones may stay plausible or unverified.
- **Steelman each rejected option.** Write its strongest case, in its advocates' terms.
- **Consider the opposite.** Search for evidence that would disprove the leading option.
- **Premortem.** "It's a year later and this decision failed. Why?" List the reasons before discussing them.
- **Outside view.** Find a reference class of similar efforts and its base rates, then adjust for specifics.
  Plan for the bad tail.
- **Inversion.** What would guarantee failure? Is the design doing any of it?
- **Motive check.** Would we pick this if it weren't new, fashionable or good for a CV?
- **Requester-preference guard.** Restate the requester's preferred option as a neutral question ("Is X the best
  fit for these drivers?") and evaluate it like any other option.

## 6. Climb the verification ladder only as far as the risk needs

| Rung | Retires | Stop when |
|---|---|---|
| Primary-source check (always) | "Does it exist and do what we think, for our version?" | Each deciding fact confirmed in a current primary source, with URL and date |
| Napkin maths | "Is this even plausible at our scale?" | Orders of magnitude are clearly fine or clearly not |
| Spike | One specific unknown | Its exit criterion (question, threshold, time box) is met; then throw it away |
| Active benchmark | Performance claims | You can name the limiter, and the result reproduces |
| Prototype / load test | End-to-end behaviour under realistic load | Scenario response measures met or missed |
| Formal model | New concurrency, replication or recovery protocols | Model checked for the properties that matter, including liveness |
| Fitness functions / chaos | Drift and resilience over time | Running continuously in the pipeline or on a schedule |

Details and checklists: [references/verification-ladder.md](references/verification-ladder.md).

## 7. State confidence and gaps honestly

This is the **shared confidence rubric**; the decision, proposal and domain skills point here. Rate facts,
forecasts and the recommendation separately:

| Level | Facts | Forecasts (load, cost, effort) | Recommendation |
|---|---|---|---|
| **High** | Fit-for-claim sources, checked at task time, corroborated as the claim class requires, no credible contrary evidence | Based on a reference class with base rates, or your own measured trend, and the case sits inside it | Deciding facts are high, and the choice holds across the plausible forecast range |
| **Medium** | Fit-for-claim but single, or applicability partly unverified | Analogous reference class, adjusted with stated assumptions | Holds for the likely case, flips at a named, plausible threshold |
| **Low** | Indirect, stale, unverified or conflicting | Judgement without a reference class | Flips within the plausible range, or rests on a low-confidence fact |

A Low or Medium rating on a deciding fact (single source, or applicability partly unverified) is a finding, not
an exit: if a check you can run (docs, source, a spike, a benchmark, asking the
vendor) would raise a deciding fact, run it before you recommend. Low stays only for what no available means can
settle (a future load, data you have no access to, a fact only the owner knows), and then the recommendation is
conditional ("if X, A; if not, B", each branch verified) or the owner is asked for that fact.

Give a numeric probability only when it comes from an explicit reference class or a calibrated forecasting
record, and name that class. Then:
- Say what evidence would change your mind, phrased so it can be checked.
- Include a **"Could not verify"** list only for what remains after you tried every available route: the claim,
  what you tried, why it can't be settled (paywall, no access, no data yet), and the risk if it's wrong. Nothing on
  it may carry the recommendation; if a deciding fact lands there, keep researching, go conditional, or ask.
- **Before accepting pushback**, write down your claims, evidence and confidence; then change only what new
  evidence or argument overturns. This prevents both caving and digging in.

## 8. Know the model-specific failure modes

If you are a language model, or are relying on one:
- **Self-review without external feedback isn't verification**; it can make answers worse. Check against
  tools, documents, tests, registries or measurements.
- **Factor the checks.** Draft, list verification questions, answer each one independently of the draft (by
  retrieval for anything factual), then revise (chain-of-verification).
- **Expect sycophancy.** Models drift toward the requester's stated view, more so when it's stated confidently.
- **Debate isn't a verifier.** Multi-agent debate is not reliably better than sampling several independent
  answers. It helps mainly when the agents hold different evidence.
- **Make implicit best practices explicit** before answering a trade-off question.

More: [references/llm-failure-modes.md](references/llm-failure-modes.md).

## 9. Deliver an evidence ledger

The research output is a ledger, not prose ([template](references/source-grading.md#ledger)):

| Decision question | Decision it can change | Claim | Source / grade | Exact passage or measurement | Applicability limits | Checked | Status |
|---|---|---|---|---|---|---|---|

Add a **search log** (queries, where, date, what was rejected) and the **unresolved-evidence** list (only what no
available means could settle, with what was tried; nothing on it carries the recommendation). Every
sentence of the recommendation must map to ledger rows. This skill owns the provenance fields; render
citations in the output's or repository's convention (`architecture-proposal-writing`).

## Pitfalls

- **Passive benchmarking.** You benchmark A, actually measure B, and conclude C.
- **Averaging away the tail.** A mean overrun hides the one-in-six disaster.
- **Citation laundering.** A blog quoting a vendor is still the vendor. Trace claims to their origin.
- **Garden of forking paths.** Choosing the benchmark configuration after seeing the results.
- **Endless research.** Without a stopping rule, research becomes procrastination. The opposite failure is
  worse and more common: stopping at the first adequate option, or shipping with a checkable fact unchecked.

## Done when

- [ ] Option list includes keep-current or configure-what-exists, the built-in or owned route, and the main
  alternatives; each finalist checked against this situation; chosen option verified to work here (all tiers).
- [ ] Research plan written; every question tied to a decision it could change; stopping rule by claim class.
- [ ] Each material claim has a fit-for-claim, graded source with year; volatile facts have URL plus date checked.
- [ ] Papers pinned to exact version; corrections, retractions and replications checked.
- [ ] Evidence ledger, search log and unresolved list delivered; every recommendation sentence maps to rows.
- [ ] Every package, API, flag, version and limit verified against a live source; anything unverifiable is listed
  with what was tried and does not carry the recommendation.
- [ ] Assumption register, steelmen and premortem done for Standard and Heavy tiers.
- [ ] Verification taken to the rung the risk needs; spikes had exit criteria.
- [ ] Confidence (facts, forecasts, recommendation) per the rubric, flip conditions and "Could not verify" (non-deciding items only) stated.

## References

- [references/source-grading.md](references/source-grading.md): claim-to-source matrix, grading axes, recency, provenance fields, evidence ledger, confidence rubric notes.
- [references/finding-and-reading-papers.md](references/finding-and-reading-papers.md): where to search, three-pass reading, version and status checks, judging validity.
- [references/debiasing-toolkit.md](references/debiasing-toolkit.md): each technique with procedure, evidence and when not to use it.
- [references/verification-ladder.md](references/verification-ladder.md): napkin maths to chaos, with checklists.
- [references/llm-failure-modes.md](references/llm-failure-modes.md): failure modes, evidence and mitigations.
