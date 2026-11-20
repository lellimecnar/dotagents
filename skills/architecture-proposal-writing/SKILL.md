---
name: architecture-proposal-writing
description: Use when presenting an architecture recommendation to a decision owner. Answer-first brief plus ADR.
metadata:
  version: "0.1.0"
  status: draft
  owner: maintainer
---

# Architecture proposal writing

How to present an architecture recommendation so a busy decision owner, including one with ADHD, can decide
in minutes and dig deeper only if they choose to. The reasoning comes from `architecture-decision-making` and
the evidence from `architecture-research-and-verification`. This skill is about shape. For general
ADHD-friendly output rules (next action first, numbered steps, specific time estimates, no preamble), follow the
shared `i-have-adhd` skill. This skill doesn't repeat them.

## When to use

- Any recommendation the owner must approve, or choose between options for.
- Writing or updating a decision record (ADR).
- Not for a two-way-door note: one or two lines with the decision, the alternatives it beat and why, how it was
  checked to work, and a revisit trigger are enough.

## The values this protects

| Value | Means | Violation looks like |
|---|---|---|
| **Owner's attention** | Decide from the first screen | Context and method before the answer |
| **Decision quality** | Trade-offs visible, not hidden | Only benefits; rejected options missing |
| **Honesty** | Precision matches evidence | Weighted totals to two decimals; uncited claims |
| **Durability** | The why survives the conversation | Rationale only in chat |

## Two artifacts, not one

1. **Decision brief**: one to three screens, answer first, for the owner's yes/no. It is disposable once decided.
2. **Decision record (ADR)**: the durable record in the repository. Use the repository's existing convention
   (directory, numbering, template). If there is none, use MADR with a Y-statement summary. The agent writes
   status **Proposed**; the decision owner accepts it. Supersede old records rather than editing them.

The brief links to the ADR. The research behind it meets `due-diligence-before-deciding`: brevity shapes the
brief, never the homework. Skeletons for both: [references/templates.md](references/templates.md).

## Brief structure

**Compact core** (always, in this order; this is what must fit in one to three screens):
1. **Recommendation and ask.** Title phrased as the answer ("Use the existing database for background
   jobs"), the Y-statement ("In the context of …, facing …, we recommend … to achieve …, accepting …"), then
   **What I need from you**: lettered decisions (only what the owner alone can decide or know; every option
   offered is already researched and viable), each with the recommended answer and what happens without an
   answer (see "Silence is not approval"). Reversibility on the same line.
2. **Decisive comparison**: an options table, five rows at most, ✓ / ~ / ✗ cells with a short reason, keep-current
   as a column, then the **deciding factor in words**.
3. **Top three risks**, each with mitigation or early signal.
4. **Cost range** with assumptions and date checked.
5. **Confidence and unknowns**: low / medium / high for facts, forecasts and the recommendation, per the shared
   rubric in `architecture-research-and-verification`. Keep evidence confidence apart from forecast uncertainty;
   no bare percentages. Add what would change the decision, and "Could not verify" (only what no available means could settle, what
   was tried, and why the recommendation holds without it).
6. **Next action**: who does what next.

**Optional sections**, only when material, linked or after the core: one focal diagram; contested-choice boxes
(one per contested choice: what, why, strongest rejected case, what it doesn't solve, fallback); evolution
path (triggers → next step); details (full alternatives, evidence ledger, napkin maths); tiered reading.

## Silence is not approval

An automatic "if no answer by <date>, I'll do X" default is allowed only for decisions the owner has explicitly
delegated that are reversible and bounded in risk. For anything else, no answer means **hold**: keep the
current state, and escalate if the delay itself carries risk. One-way, security, privacy, spending and
destructive decisions need affirmative approval. *How to spot the violation:* a default that would commit
money, delete data or change a security boundary on a timeout.

## Rules, with the reason for each

**Answer first.** The first two lines carry the recommendation and the ask. Readers mostly scan; frontloading
means they get the point however little they read ([sources](references/sources.md#answer-first)).
*Don't apply* when the owner asked for an explanation or teaching walk-through. Even then, lead with a summary.

**Headings state conclusions**, e.g. "The database queue wins on running cost", not "Queue options". A scanner reading only
headings gets the argument.

**Options tables are qualitative.** Use ✓ / ~ / ✗ (or H/M/L) with a phrase per cell, plus the one or two
deciding criteria in words. No weighted decimal totals: they imply precision the inputs don't have, and the
winner often flips with the weights. If you used weights, show the sensitivity ("A wins unless cost weight
exceeds 40%"). No radar charts: area depends on axis order and they're hard to compare. Use a table, or bars or
dots per criterion ([sources](references/sources.md#tables-and-charts)).

**Every contested choice gets a box** with the strongest argument for each rejected option. A proposal with
only benefits reads as advocacy; the steelman shows the owner the real trade-off.

**Diagrams answer a question.** The brief carries at most one focal diagram, picked by the owner's question:
container (deployable pieces), sequence (order and failure paths), deployment (what fails together) or data
flow with trust boundaries (security). Add a context diagram first only when the audience lacks shared
context. Further views go in the ADR or an appendix. Use the repository's or output's existing diagram
convention; otherwise pick a text-native format confirmed to render in the target, and check that it renders.
Mermaid is a reasonable fallback where the target supports it. Every diagram has a title, a one-sentence
purpose, one abstraction level and consistent names. Next to it, write prose stating its conclusion, the
principal relationships and any material failure or trust boundaries, so the diagram is never the only
carrier of meaning ([diagram selection](references/diagram-selection.md)).

**Cost as ranges with assumptions.** Give low–high ranges, the assumptions (load, region, tier, operator hours)
and the date the prices were checked, with a link to the pricing page. Include operator time, not just the bill.
A cost-versus-load curve with one line per option and the crossover marked shows "fits today, room for
tomorrow" well (a design suggestion, not a researched standard).

**Cite per claim, in one renderer.** The provenance fields (source, year, version, grade, checked date) come
from `architecture-research-and-verification`; this skill only renders them. Use the repository's or output's
citation convention and one renderer per artifact. Put the citation next to the claim it supports. Source
every claim; one that still cannot be sourced after trying goes to "Could not verify" only when no available
means can settle it, and it must not carry the recommendation. Never invent a link.

**Tiered optional reading.** End with three tiers, each item: link · year · source type · a "why read" of ten
words or fewer.
- **Must-read** (≤ 3, ≤ 10 minutes total): what the decision rests on.
- **Deep dive** (≤ 5): design docs, papers, benchmarks.
- **Reference** (any number): specs, API docs, templates.

**Progressive disclosure.** Keep the first screen to the decision. Use the target's native disclosure
mechanism if it has one; otherwise a short summary plus a plain linked appendix. Collapsed blocks don't
survive every renderer, PDF, email or assistive technology, so never hide essential evidence only inside one.
Open any section longer than a screen with a summary line.

**Plain language.** One idea per paragraph, at most three sentences, one point per sentence, bullets for lists.
Bold at most one key phrase per paragraph. Define a term the first time it appears, or replace it with plain words.

**Time estimates only with a basis.** Give the owner's reading or decision time in concrete units ("5-minute
read"). For work estimates that affect the decision, give a range plus assumptions ("2–3 days, assuming no
schema change"). With no basis yet, build one (napkin maths, comparable past work, a short spike) and give a range
with that basis rather than inventing precision. Ask someone else only for what only they can know, after
doing the rest, and say what you tried.

## When to break the shape

- **Big, multi-team bets** may warrant a longer narrative document (a six-page memo style) on top of the
  brief. The brief still comes first.
- **"Why build at all?"** questions: consider a press-release-and-FAQ framing that works backwards from the user.
- **Owner asks for depth**: expand the body, keep the answer-first top.

## Pre-send check

- [ ] Reading only the first two lines, does the owner know the recommendation and what they must decide?
- [ ] Compact core present: recommendation and ask, decisive comparison, top three risks, cost range,
  confidence and unknowns, next action.
- [ ] Every option presented is researched and viable; the recommended option was checked to work here against
  primary, current sources; no sentence hedges on a checkable fact; the owner can act without asking for more
  homework.
- [ ] Every lettered decision has a recommended answer; any timeout default is delegated, reversible and
  bounded; high-consequence decisions ask for affirmative approval.
- [ ] Options table is qualitative, with the deciding factor in words; keep-current is a column.
- [ ] Each contested choice has a box with the strongest rejected case and a fallback.
- [ ] Confidence given for facts, forecasts and recommendation per the shared rubric; flip conditions and
  "Could not verify" present.
- [ ] At most one focal diagram in the brief, in a format confirmed to render, with title, purpose and an
  adjacent text equivalent.
- [ ] Cost is a range with assumptions, operator time and date checked.
- [ ] Every claim cited in one renderer, from research provenance fields; optional reading tiered.
- [ ] Essential evidence is visible without collapsed UI; first screen fits on one screen.
- [ ] ADR drafted as Proposed in the repository's convention and linked.

## References

- [references/templates.md](references/templates.md): decision brief, ADR (MADR-based), Y-statement, contested-choice box, worked example.
- [references/diagram-selection.md](references/diagram-selection.md): question → diagram, format choice, example snippets, communication and text-equivalent checklist.
- [references/sources.md](references/sources.md): evidence behind each rule, with links.
