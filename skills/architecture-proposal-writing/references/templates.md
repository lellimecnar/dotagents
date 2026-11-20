# Templates

Skeletons for the decision brief, the decision record, the Y-statement and the contested-choice box,
followed by a short filled-in example. Adapt headings to the repository's conventions where they exist.

Credits: the brief structure combines the answer-first pyramid (Minto, https://www.barbaraminto.com/concept)
with the Google design-doc sections (https://www.industrialempathy.com/posts/design-docs-at-google/). The ADR
skeleton follows MADR's section structure (https://adr.github.io/madr/), written here in our own words; record
practice follows AWS Prescriptive Guidance and the GDS Way (see sources.md). The
contested-choice box adapts the "Why & What box" idea from riekelt `writing-design-docs` (MIT,
https://github.com/riekelt/technical-writer/blob/main/plugins/technical-writer/skills/writing-design-docs/SKILL.md).
The Y-statement is Zimmermann's (https://socadk.github.io/design-practice-repository/artifact-templates/DPR-ArchitecturalDecisionRecordYForm.html).

---

## 1. Decision brief skeleton

```markdown
# <Decision phrased as the answer>

**In short:** In the context of <situation>, facing <concern>, we recommend <option> to achieve <quality/goal>,
accepting <main downside>.

**What I need from you** (about <N> min):
- **A.** <decision> (recommended: <answer>). Without an answer: <hold, keep current state>.
- **B.** <delegated, reversible, bounded-risk decision> (recommended: <answer>). If no answer by <date>: <default>.

<!-- Timeout defaults only for delegated, reversible, bounded-risk decisions. One-way, security, privacy,
     spending or destructive decisions: affirmative approval; silence means hold. -->

**Reversibility:** <two-way door / costly to reverse: why> · **ADR:** <link> (Proposed)
**Confidence** (shared rubric): facts <low/medium/high> · forecasts <low/medium/high> · recommendation <low/medium/high>

| At a glance | <Recommended> | <Keep current> | <Extend existing> | <Option D> |
|---|---|---|---|---|
| Meets top scenario: <name> | ✓ <reason> | ✗ <reason> | ~ <reason> | ✓ <reason> |
| Running effort (ops hours) | ✓ <reason> | ✓ <reason> | ✓ <reason> | ✗ <reason> |
| Cost range (checked <date>) | <low–high> | <low–high> | <low–high> | <low–high> |
| Room to grow (forecast peak) | ~ <reason> | ✗ <reason> | ~ <reason> | ✓ <reason> |
| New tech to learn/operate | none | none | none | <what> |

<!-- One column per further real option found; five rows at most. -->

**Deciding factor:** <one sentence, in words>.

## Top risks
| Risk | Likelihood | Impact | Mitigation / early signal |
|---|---|---|---|
<at most three rows>

## Cost
<range; assumptions: load, region, tier, operator hours/month; prices checked <date> at <link>>

## Confidence and unknowns
**Would change this decision:** <facts or thresholds>.
**Could not verify:** <claim> (what was tried; why it can't be settled; risk if wrong; why the recommendation
holds without it). "None" when nothing.

## Next action
<who does what next; if a work estimate affects the decision, a range with its basis (napkin maths, comparable work, spike) and assumptions>

---
<!-- Optional sections below: include only when material. -->

## Picture (optional)
<one focal diagram answering a named question, in the repository's diagram convention or a format confirmed
to render; title + one-sentence purpose>
<adjacent prose: the conclusion, principal relationships, material failure or trust boundaries>

## Contested choices (optional)
<one box per contested choice: see §4>

## Evolution path (optional)
- If <trigger>, then <next step> (est. <range, assumptions>).

## Details (optional)
<Use the target's native disclosure if it has one; otherwise link a plain appendix. Keep essential evidence
visible in the core.> Alternatives, evidence ledger, napkin maths.

## Optional reading
**Must-read:** <link> (<year>, <official/paper/practitioner>): <why, ≤10 words>
**Deep dive:** …
**Reference:** …
```

## 2. Decision record (ADR) skeleton, MADR-based

```markdown
# <NNNN>. <Short title phrased as the decision>

- Status: Proposed            <!-- the decision owner changes this to Accepted -->
- Date: <YYYY-MM-DD>
- Decision owner: <role>
- Supersedes / superseded by: <link or none>

## Summary (Y-statement)
In the context of <…>, facing <…>, we decided for <…> and against <…>, to achieve <…>, accepting <…>.

## Context and problem
<two to five sentences; link the brief>

## Decision drivers
- <quality scenario with response measure>
- <constraint>

## Considered options
<!-- One entry per real option found; keep searching until no new serious candidates appear. -->
1. <Keep current / change nothing>
2. <Extend what exists>
3. <Option C>

## Decision outcome
Chosen: <option>, because <deciding reason tied to drivers>.

### Consequences
- Good: <…>
- Bad (accepted): <…>      <!-- a record with no downsides is incomplete -->

### Confirmation
<how compliance is checked: fitness function, review, test; and the review date or trigger>

## Pros and cons of the options
### <Option>
- Good: <…>
- Neutral: <…>
- Bad: <…>

## More information
<evolution triggers; links with year and source type; what would change this decision>
```

Record rules: one significant decision per record; never rewrite history, supersede it instead
(https://docs.aws.amazon.com/prescriptive-guidance/latest/architectural-decision-records/best-practices.html);
keep records next to the code (https://thoughtworks.com/radar/techniques/lightweight-architecture-decision-records).

## 3. Y-statement

> In the context of **<use case / component>**, facing **<non-functional concern>**, we decided for
> **<option>** and neglected **<rejected options>**, to achieve **<quality / benefit>**, accepting that
> **<downside / cost>**[, because **<additional rationale>**].

Use it as the first line of both the brief and the ADR.

## 4. Contested-choice box

```markdown
> **Choice:** <what we chose>
> **Why:** <deciding reason, tied to a driver>
> **Strongest case for <rejected option>:** <its best argument, in its advocates' terms; when it would win>
> **What this does not solve:** <limits>
> **Fallback if it goes wrong:** <plan; the trigger that invokes it>
```

---

## 5. Worked example (illustration only)

*Hypothetical small web application; names, numbers and judgements are illustrative, not recommendations. Real
proposals verify every fact and date-stamp every price.*

```markdown
# Use the existing Postgres database for background jobs

**In short:** In the context of a single-server web app sending ~2,000 emails and reports a day, facing the need
for reliable retries, we recommend a job table in the existing Postgres (row locking with SKIP LOCKED) to achieve
transactional enqueue with no new infrastructure, accepting that we'll need to move jobs to a dedicated queue if
volume grows roughly 50×.

**What I need from you** (2 min):
- **A.** Approve the database job queue (recommended). Without an answer I'll hold the work.
- **B.** Job history retention: 7 days (recommended) or 30 days. You delegated retention settings and this is
  reversible, so if no answer by Friday I'll use 7 days.

**Reversibility:** two-way door, because jobs sit behind one interface · **ADR:** 0012 (Proposed)
**Confidence:** facts high (locking behaviour checked in the official docs) · forecasts medium (volume from
3 months of logs, extrapolated) · recommendation high (holds across the forecast range)

| At a glance | DB job table (rec.) | Keep cron scripts | Add a message broker | Managed cloud queue |
|---|---|---|---|---|
| Retries + visibility | ✓ built in | ✗ silent failures | ✓ | ✓ |
| Enqueue in same transaction | ✓ | ✗ | ✗ needs outbox | ✗ needs outbox |
| New things to operate | none | none | broker + backups + monitoring | account, IAM, SDK |
| Running effort | under 1 h/month (existing backups, monitoring) | under 1 h/month, but failures unseen | 2–4 h/month (patching, backups, monitoring) | 1–2 h/month (IAM, SDK upgrades) |
| Room to grow (forecast peak) | ✓ 50× today's volume in a load test | ✗ | ✓ | ✓ |

**Deciding factor:** it meets the retry and visibility scenario with zero new components, and enqueueing in the
same transaction removes a whole class of lost-job bugs.

## Top risks
| Risk | Likelihood | Impact | Mitigation / early signal |
|---|---|---|---|
| Table bloat from high job churn | medium | slower pickup | retention job; bloat alert |
| Long jobs hold row locks | low | stalled queue | job timeout; p95 pickup-delay alert |
| Volume outgrows the table | low | migration work | fallback below; pickup-delay trigger |

## Cost
No new bill (runs on the existing database server); under 1 h/month operator time. The broker alternative adds
2–4 h/month plus hosting at <low–high>/month (prices checked <date> at <pricing link>).

## Next action
I build the job table and worker behind the queue interface: 2–3 days, based on a comparable past job
migration, assuming no schema change to existing tables.

> **Choice:** database job table
> **Why:** meets the drivers; no innovation token spent
> **Strongest case for a broker:** many independent consumers, replay and high throughput; wins if we add
> event fan-out to other services
> **What this does not solve:** cross-service events; very high churn (table bloat)
> **Fallback:** swap the queue implementation behind the interface; trigger = p95 job pickup delay above 30 s
> for a week, or bloat alerts

**Evolution path:** if pickup delay breaches the target → tune (indexes, batch size); if it is still breached →
move to a dedicated queue behind the same interface (est. 2–3 days, assuming the interface is unchanged).

**Optional reading**
- Must-read: PostgreSQL SELECT docs, locking clause (official) https://www.postgresql.org/docs/current/sql-select.html: SKIP LOCKED semantics
- Deep dive: Transactional outbox pattern (practitioner) https://microservices.io/patterns/data/transactional-outbox.html: why same-transaction enqueue matters
- Deep dive: Choose Boring Technology (2015, practitioner) https://mcfunley.com/choose-boring-technology: innovation tokens
```
