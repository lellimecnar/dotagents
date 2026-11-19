# Debiasing toolkit

Techniques to run as explicit steps, with the procedure, the evidence behind each, and when not to use it.

**Why steps, not awareness.** A lecture-only debiasing treatment was ineffective
(https://arxiv.org/abs/2111.04362). A hands-on workshop improved reasoning in 10 of 12 teams
(https://arxiv.org/abs/2206.14701), and in a controlled experiment anchoring and optimism decreased
significantly (https://arxiv.org/abs/2502.04011). Kahneman, Lovallo & Sibony: "awareness of the effects of
biases has done little to improve the quality of business decisions", so use a checklist
(https://hbr.org/2011/06/the-big-idea-before-you-make-that-big-decision).
**Caveat:** this evidence is small-N and mostly from one research group, and the field's mapping study finds
mitigation research scarce (https://arxiv.org/abs/1707.03869). The techniques are cheap, so use them, but don't
overclaim their effect.

## Biases that matter most in architecture

| Bias | Shows up as | Countered by |
|---|---|---|
| Anchoring | Preferring "the first architectural solution that they came up with" | Options before preference; consider the opposite |
| Optimism / planning fallacy | Estimates that ignore how similar efforts went | Outside view; premortem; plan for the tail |
| Confirmation | Searching only for support | Consider the opposite; disconfirming search terms |
| Familiarity / speed | Choosing what you know or what ships fastest (https://arxiv.org/abs/2309.14164) | Steelman; option checklist |
| Satisficing | Stopping at the first adequate option (https://research.vu.nl/en/publications/software-designers-satisfice/) | Mandatory option set |
| Sunk cost / endowment | Persisting because of past investment | "If we hadn't already, would we choose this now?" |
| Groupthink / affect | Team in love with its proposal | Independent written input; dissent recorded |
| Résumé-driven | Trend chasing (60% of hiring professionals say trends shape job offers) https://portal.fis.tum.de/en/publications/resume-driven-development-a-definition-and-empirical-characteriza/ | Motive check; make hiring an explicit driver if real |

Biases have been linked to classes of architectural technical debt (https://arxiv.org/abs/2309.14175). One
small experiment (16 students, 20 practitioners) found its practitioner group more susceptible than its
students; the authors suggest attachment to their own systems as a possible cause
(https://arxiv.org/abs/2502.04011). Use this as a reason to run the checks, not as a population estimate.

## Techniques

### 1. Options before preference
- **Do:** write the full option list (keep-current, extend, buy/rent/self-host/build, simplest, unconventional)
  before scoring or favouring any option.
- **Evidence:** checklist Q5, "insist that people submit at least one or two alternatives" (HBR 2011, above);
  anchoring findings (https://arxiv.org/abs/2502.04011).
- **Not when:** the choice is forced. Say so instead.

### 2. Assumption register: "what would have to be true?"
- **Do:** for the leading option, list each assumption it depends on, with columns: assumption · how to test ·
  status (verified / plausible / unverified) · impact if false. Test the high-impact unverified ones first.
- **Evidence:** architectural assumptions are often implicit and undocumented; practitioner surveys led to an
  assumption-documentation framework (https://www.sciencedirect.com/science/article/abs/pii/S0164121215002824,
  abstract paywalled). Making background assumptions explicit before answering cut SE-dilemma bias sensitivity in
  language models by 51% on average (https://arxiv.org/abs/2604.16756).
- **Not when:** a light-tier decision. One line of key assumptions is enough.

### 3. Consider the opposite
- **Do:** ask "what evidence would show this is wrong?" and go looking for it. Use search terms like
  "<option> problems", "migrating away from <option>", "<option> postmortem".
- **Evidence:** a validated corrective for biased judgement (Lord, Lepper & Preston 1984)
  https://pubmed.ncbi.nlm.nih.gov/6527215/ ; HBR checklist "Did you actively look for information that would disprove…?"

### 4. Steelman each rejected option
- **Do:** write the strongest case for each rejected option, in its advocates' terms, and say what would make it
  the right choice. The case goes into the proposal's contested-choice box.
- **Evidence:** structured dissent (devil's advocacy and dialectical inquiry) has a meta-analytic evidence base
  (Schwenk 1990) https://www.sciencedirect.com/science/article/pii/074959789090051A
- **Not when:** an option fails a hard constraint. One line saying which constraint is enough.

### 5. Premortem
- **Do:** "Imagine it's a year from now and this decision has failed badly." Each participant (or, for a single
  agent, a separate pass) writes every reason independently *before* any discussion. Merge, rank, and turn the
  top reasons into mitigations, spikes or fitness functions.
- **Evidence:** Klein, HBR 2007 https://hbr.org/2007/09/performing-a-project-premortem . Writing independently first
  is what protects dissent. Don't cite the "30%" figure as settled (see source-grading.md §4).
- **Not when:** used as a catch-all veto. It produces a risk list, not a decision. If nobody acts on its outputs,
  it is theatre.

### 6. Outside view (reference-class forecasting)
- **Do:** (1) pick a reference class of similar past efforts, broad enough to be meaningful and narrow enough to be
  comparable; (2) find the distribution of outcomes; (3) place this effort on it, then adjust for specifics.
- **Evidence:** Flyvbjerg https://arxiv.org/abs/1302.3642 . IT risk is fat-tailed: across 1,471 projects the mean
  overrun was 27%, but about one in six averaged 200% (https://arxiv.org/abs/1304.0265).
- **Not when:** truly novel work with no comparable class. Widen the interval instead of inventing precision.

### 7. Inversion
- **Do:** ask "how would we guarantee this fails?" (single points of failure, untested restores, an unowned
  component, a dependency nobody can operate). Then check whether the design does any of it.
- **Note:** this is a practitioner heuristic with no specific study cited here. It's cheap and
  complements the premortem.

### 8. Motive check
- **Do:** "Would we choose this if it weren't new, fashionable or CV-worthy?" If hiring or retention genuinely
  matters, state it as a driver instead of disguising it as technical merit.
- **Evidence:** https://portal.fis.tum.de/en/publications/resume-driven-development-a-definition-and-empirical-characteriza/

### 9. Separate reasoning from reflection
- **Do:** after the design pass, run a separate reflection pass (another reviewer, a separate session, or at least
  a different time) that critiques the reasoning, not just the result.
- **Evidence:** external reflection improved design discourse "under certain preconditions" (Razavian et al. 2016)
  https://pure.tue.nl/ws/files/22224469/10.1002_smr.1776.pdf

### 10. Score criteria independently before the holistic judgement
- **Do:** rate each option on each criterion separately (✓ / ~ / ✗ with a reason), and only then form the overall view.
- **Evidence:** Mediating Assessments Protocol (summary) https://theuncertaintyproject.org/tools/the-mediating-assessments-protocol

### 11. Know when intuition is fine
Trust expert intuition in regular environments with fast feedback. Architecture often lacks both (rare
decisions, delayed feedback), which argues for explicit analysis on one-way doors. Recognition-primed decisions
remain right for urgent, familiar calls; record them and revisit.
https://psycnet.apa.org/record/2009-13007-001 ; https://journals.sagepub.com/doi/10.1518/001872008X288385

## The HBR decision-quality checklist (as review prompts)

The article proposes 12 questions, asked by someone independent of the team. Examples: Has the team fallen in love with its proposal? Were there dissenting opinions? Were credible
alternatives considered ("at least one or two alternatives to the main recommendation")? Did you actively look
for information that would disprove the recommendation? And the sunk-cost test: "If I personally hadn't decided
to build the plant in the first place, would I invest…?" Read the article for the full list.
https://hbr.org/2011/06/the-big-idea-before-you-make-that-big-decision (full-text copy:
https://faculty.washington.edu/pathaksd/BBUS549/Required%20Readings/Before%20You%20Make%20that%20Big%20Decision.pdf)

<a id="sources"></a>
## Sources
Inline above.
