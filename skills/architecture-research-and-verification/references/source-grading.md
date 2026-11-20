# Source grading

How much weight a source deserves, and how to record it so a reader can check it.

## 1. Claim-to-source matrix

Source type is not a universal quality rank. Ask what kind of claim you are making, then which evidence is fit
for that claim. A source that is authoritative for one claim class can be weak for another.

| Claim class | Fit-for-claim evidence | Weak evidence for this class | Typical failure | Check |
|---|---|---|---|---|
| **Current product behaviour**: features, limits, APIs, versions, prices, support windows | Versioned official docs, specifications, source code, registries, release notes; your own experiment | Blog posts, model memory, old answers | Docs lag the code; marketing pages pose as docs; wrong version | Version and date match what you'll run |
| **Comparative performance** | Reproducible benchmark on the target workload, ideally rerun by you | Vendor docs and vendor benchmarks of their own product; papers on a different workload or hardware generation | Tuning asymmetry, cold vs warm caches, wrong workload, cherry-picked configs | Gregg's checklist (verification-ladder.md); does the workload match yours? |
| **Causal or effect claim**: bias, productivity, failure rates, the effect of a practice | Empirical research using a method suited to the question, judged against that method's empirical standard; reviews and replications | Single anecdotes; surveys of opinion presented as effect sizes | Small samples, student subjects, context mismatch, unreplicated, forking paths | finding-and-reading-papers.md |
| **Operating experience**: toil, failure modes, migration cost, what it is like to run | Postmortems and experience reports, corroborated by independent reports | A single success story; vendor case studies | Survivorship, one context generalised, résumé or employer incentives | Who wrote it, their scale and context, date, whether others with different origins report the same |
| **Existence or discovery**: "is there a tool for X?" | Anything, including vendor pages and listicles, as a lead only | — | Treating discovery as endorsement | Trace each lead to a fit-for-claim source before relying on it |

What the cited sources do and don't support: Garousi, Felderer & Mäntylä (IST 2019) give grey-literature
appraisal criteria and a multivocal-review procedure https://arxiv.org/abs/1707.02553 ; the ACM SIGSOFT
Empirical Standards give method-specific criteria for empirical studies https://www2.sigsoft.org/EmpiricalStandards/ ;
evidence-based software engineering (Kitchenham, Dybå & Jørgensen, ICSE 2004) argues for integrating research
evidence with practical experience and stakeholder context
https://ebse.webspace.durham.ac.uk/ebse-bibliography/evidence-based-software-engineering/ . None of them
establishes a single ordering of source types; the matrix above is this skill's synthesis (judgement).

**Independence.** Two sources are independent only when they don't share an origin, author incentive or data
set. Count origins, not links: a blog quoting a vendor benchmark is the vendor benchmark (see section 4).

**Stopping by claim class and risk.** One versioned primary source settles existence, syntax and limits for
the version you'll run. Contentious operational claims need the primary source plus independent
corroboration. Effect claims need a suitable review or replication; if none exists after searching, say the evidence is thin,
lower confidence, and do not let that claim carry the recommendation.

## 2. Grading axes (from Garousi Guideline 11)

Grade every source on these axes, independently of its claim-class fit. Authority, method, objectivity and
date matter most; vendor material usually fails on objectivity even when it is fit for an existence claim.

| Criterion | Question |
|---|---|
| Authority of the producer | Does the author or organisation have the expertise and the first-hand position to know? |
| Methodology | Is there a stated method, data or reproducible steps? |
| Objectivity | Does the author benefit from the conclusion? Are downsides discussed? |
| Date | When was it written, and has the subject changed since? |
| Novelty | Does it add anything beyond what primary sources say? |
| Impact | Is it cited, linked or relied on by others? |
| Outlet control | Was there any editorial or review gate? |

https://arxiv.org/abs/1707.02553

## 3. Recency checks

- Record **publication year** for every source and **date checked** for anything volatile (features, limits,
  prices, versions, maintenance activity, licence).
- For technology choices, also check maintenance signals at the primary source: recent releases, open security
  advisories, the stated support window, the licence as published today.
- Treat dated classics as durable *ideas*, not current *facts*. Example: the widely copied latency-numbers gist
  is headed "~2012". Use it for orders of magnitude, then re-measure. https://gist.github.com/jboner/2841832
- Adoption-signal sites (e.g. a technology radar) are one organisation's opinion, so grade them as grey literature.
  https://thoughtworks.com/radar

## 4. Tracing claims to their origin

- A post quoting a number should cite where it came from. Follow the chain to the first source and grade *that*.
- Check that the origin says what the quote claims. Example: the premortem's often-quoted "30% improvement" traces
  to a 1989 study whose abstract attributes the effect to outcome *certainty*, not the time shift. Use premortems,
  but don't quote the number as settled.
  https://lmscontent.embanet.com/USC/PPD554/Week10/PPD554_W10_HBR_Klein_Performaing_a_Project_Premortem.pdf ;
  https://www.researchgate.net/publication/227768493_Back_to_the_future_Temporal_perspective_in_the_explanation_of_events

## 5. Provenance fields (owned here; rendering is not)

For every material claim, capture these fields. How they are rendered (footnotes, inline links, a references
section) belongs to the output's or repository's citation convention, applied by `architecture-proposal-writing`.
Use one renderer per artifact.

| Field | Content |
|---|---|
| Claim | The exact statement relied on |
| Claim class | From the matrix in section 1 |
| Source | Title, author or organisation, year, URL, and exact version (doc version, DOI or arXiv version, commit) |
| Grade | Fit for claim (yes / partial / no) plus authority, method, objectivity, date |
| Support | The exact passage quoted, or the measurement with its configuration |
| Applicability limits | Version, workload, scale or population differences from your case |
| Checked | Date checked, for anything volatile |
| Status | verified / plausible / unverified / contradicted |

Mark unsupported statements as unsourced or move them to a "Could not verify" list rather than presenting them as
fact, after trying the other routes for that claim class. An unverified statement never carries a recommendation. Never invent a link.

<a id="ledger"></a>
## 6. Evidence ledger, search log and unresolved list

The research deliverable is a ledger whose rows carry the provenance fields, keyed to the decision:

```markdown
| # | Decision question | Decision it can change | Claim | Source / grade | Exact passage or measurement | Applicability limits | Checked | Status |
|---|---|---|---|---|---|---|---|---|
| E1 | Can the existing database run the job queue? | DB queue vs broker | Row locks can skip locked rows | <official docs, version> / fit, high authority | "<quoted passage>" | Applies from version <n> | <YYYY-MM-DD> | verified |
```

- **Search log:** query, where searched, date, what was found and rejected (and why). It lets a reviewer see
  whether disconfirming searches were run.
- **Unresolved evidence:** claim, what was tried, why no available means can settle it (paywall, no access, no
  data yet; conflicts only after tracing each side to its origin), risk if wrong, what would resolve it. Nothing
  on this list carries the recommendation.
- **Traceability rule:** every sentence of the recommendation cites one or more ledger rows (e.g. "[E1, E4]").
  A sentence with no row is opinion and must be labelled as judgement.

## 7. Applying the confidence rubric

The rubric is in SKILL.md step 7. Notes for applying it:
- Rate facts, forecasts and the recommendation separately. High-confidence facts don't make a forecast high
  confidence.
- The recommendation's level is capped by the lowest-confidence fact it depends on, unless the choice holds
  even if that fact is wrong.
- Numeric probabilities need an explicit reference class (e.g. "of N comparable migrations we found, M overran
  by more than 50%") or a calibrated forecasting record. Without one, use the words, not a number.

## 8. Attribution

Scoring sources into "start here / recommended / further reading" tiers with a last-verified date per source is
adapted from depuschm/architecture-guidelines (CC-BY-4.0, https://github.com/depuschm/architecture-guidelines).
The unsourced-statement marker idea comes from riekelt `writing-design-docs` (MIT,
https://github.com/riekelt/technical-writer/blob/main/plugins/technical-writer/skills/writing-design-docs/SKILL.md).
Ideas adapted, no text copied.
