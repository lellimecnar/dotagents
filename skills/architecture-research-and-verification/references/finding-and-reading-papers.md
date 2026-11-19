# Finding and reading papers

## 1. When papers are worth the time

Papers earn their cost when the decision is a Standard or Heavy tier and hinges on a mechanism (consistency,
replication, scheduling), a measured effect (bias, productivity, failure rates), or a known failure class.
For "which version supports feature X", go to primary docs instead.

## 2. Where to search

Re-run the searches at task time. These are durable locations, not a list of results.

| Kind | Where | Notes |
|---|---|---|
| Scholarly search | Google Scholar https://scholar.google.com · Semantic Scholar (API with abstracts and open-access links, rate-limited) https://api.semanticscholar.org/graph/v1/paper/ · dblp https://dblp.org | Use citation counts and "cited by" to find follow-ups and rebuttals |
| Preprints | arXiv listings (cs.SE, cs.DC, cs.DB, cs.CL) and its export API http://export.arxiv.org/api/query | Not peer-reviewed. Check for a published version |
| Publishers | ACM DL https://dl.acm.org · IEEE Xplore https://ieeexplore.ieee.org · USENIX (open access) https://www.usenix.org/conference/osdi24/technical-sessions | Systems venues: OSDI, NSDI, ATC, FAST, VLDB, SIGMOD. SE venues: ICSE, FSE, ICSA, ECSA |
| Curated | Papers We Love https://github.com/papers-we-love/papers-we-love · The Morning Paper archive https://blog.acolyer.org/archives | Fast triage of classics |
| Architecture research | ICSA programmes via https://conf.researchr.org | Newest decision-making and ADR research |

Search tactics:
- Start from a known good paper and walk both directions: its references (backward) and papers citing it (forward).
- Search for the rebuttal too: "<topic> limitations", "<topic> replication", "revisiting <paper>".
- For a survey, follow a systematic-review procedure when the stakes justify it (Kitchenham & Charters)
  https://researchgate.net/publication/302924724_Guidelines_for_performing_Systematic_Literature_Reviews_in_Software_Engineering ;
  include grey literature with grading (Garousi) https://arxiv.org/abs/1707.02553

## 3. Read in three passes (Keshav)

1. **Pass 1 (5–10 min):** title, abstract, introduction, section headings, conclusions, references.
   Decide: relevant? valid-looking? worth pass 2?
2. **Pass 2:** grasp the content but not every detail. Note the claims you'd rely on and the references worth chasing.
3. **Pass 3 (only for the paper the decision rests on):** re-derive the work. Challenge each assumption and
   look for the cases where it would fail in your context.

https://www.cs.princeton.edu/courses/archive/fall18/cos561/papers/HowToRead05.pdf

## 4. Pin the version and status

Before relying on a paper (judgement-level procedure; no single source prescribes it):
- Record the DOI or arXiv identifier **and version** (e.g. v2), and whether it is a preprint or the
  peer-reviewed version. If both exist, prefer the published version and note differences that matter.
- Check publication status on the publisher's page and the DOI record: corrections, expressions of concern,
  retractions.
- Search forward citations for replications, failed replications and rebuttals ("<title> replication",
  "revisiting <title>").
- Save the exact version you used (the file or a permanent link), so a reviewer reads what you read.

## 5. Judging validity

| Check | Question | Source |
|---|---|---|
| Research strategy | Lab experiment, field study, survey, case study, simulation? Each trades realism, precision and generality | Stol & Fitzgerald, ABC framework https://dl.acm.org/doi/10.1145/3241743 |
| Validity types | Construct, internal, external and conclusion validity; reliability; reproducibility | ACM SIGSOFT Empirical Standards https://www2.sigsoft.org/EmpiricalStandards/ |
| Sample and subjects | How many, students or practitioners, one organisation or many? | e.g. the 2025 debiasing experiment had 16 students and 20 practitioners https://arxiv.org/abs/2502.04011 ; the 2022 workshop study, from the same group, 12 teams https://arxiv.org/abs/2206.14701 |
| Preregistration and analysis plan | Were hypotheses and analysis fixed in advance (preregistration or registered report)? | ACM SIGSOFT Empirical Standards (registered-reports supplement) https://www2.sigsoft.org/EmpiricalStandards/ |
| Data and code | Are data, code and materials available so the analysis can be rerun? | ACM SIGSOFT Empirical Standards (open-science supplement) https://www2.sigsoft.org/EmpiricalStandards/ |
| Venue and review | Peer-reviewed venue or preprint? Workshop or main track? | — |
| Replication | Has anyone reproduced it, or found the opposite? | e.g. self-correction: positive https://arxiv.org/abs/2303.17651 vs negative https://arxiv.org/abs/2310.01798 |
| Benchmarks in the paper | Any of the 22 "benchmarking crimes"? | van der Kouwe et al. https://arxiv.org/abs/1801.02381 |
| Analysis flexibility | Were analysis choices made after seeing the data? | Gelman & Loken https://stat.columbia.edu/~gelman/research/unpublished/forking.pdf |
| Context match | Same scale, workload, team size and constraints as yours? | — |
| Incentives | Who funded it, and does the author sell the thing studied? | Garousi objectivity criterion https://arxiv.org/abs/1707.02553 |

Bias research in software engineering is broad but thin on mitigation (65 articles, 37 biases mapped). Treat
any single debiasing result as provisional. https://arxiv.org/abs/1707.03869

## 6. Writing it up

For each paper used in a proposal, record the provenance fields (source-grading.md section 5): title, authors,
year, venue or "preprint", exact version, status checked, the specific finding you rely
on (with a number when there is one), its main limitation, and the link. Put it in the deep-dive reading tier
unless the decision rests on it, in which case it is must-read.
