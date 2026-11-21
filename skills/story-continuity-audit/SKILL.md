---
name: story-continuity-audit
description: Use when checking story canon/continuity against prose. Cited ledgers.
metadata:
  version: "0.1.1"
  status: draft
  owner: maintainer
---

# Story continuity audit

The method of a continuity editor or canon keeper for any work of fiction: keep the manuscript, the
story bible and the reader's knowledge in agreement, and when they disagree, say exactly where, which
kind of disagreement it is, and the smallest fix. Two modes share one method: a **chapter check**
(one revision or chapter range) and a **whole-canon audit** (a story bible or packet set against the full
manuscript). Sources `[S#]` resolve in `references/sources.md`.

Research basis: professional fiction editing tracks characters, places, events and timelines while
preserving authorial intent and querying uncertain issues rather than silently rewriting [S1–S6]. Readers
build situation models across time, space, entities, causation and goals [S10]. Dramatic irony depends on
a controlled gap between reader and character knowledge [S8–S9], and editors tend to project their own
later knowledge backward (curse of knowledge) [S11]. Story order is not discourse order [S12].

## When to use

Canon checks after a revision, continuity audits, story-bible maintenance, timeline reconciliation,
reader-versus-character knowledge checks, publication-readiness canon audits, and read-side mirror
verification. Not for drafting prose; hand prose fixes to the author.

## Authority stack (narrowest authoritative evidence per question)

1. Binding owner decisions, exact-text locks and protected spans govern intent.
2. Current manuscript prose at a frozen revision proves what happens and what readers can see.
3. The story bible records the maintained interpretation and future constraints.
4. Search mirrors and indexes (vector stores, knowledge graphs) retrieve the bible; they are not authority.
5. A reader-position tool (an e-book reader or QA tool bounded to a chapter) retrieves only reader-visible
   prose; it is not canon.
6. Outlines and plans describe intended beats until current prose disproves them.

Absence from search is never proof of absence.

## Method

1. **Freeze authority.** Record the manuscript revision (and any built artifact's revision). Read the
   binding plan and decided no-ops. A lock, withheld-lore rule, protected span or no-op outranks a
   plausible cleanup. In read-only audits, keep the repository untouched; scratch files live elsewhere.
2. **Prove scope.** Build newline-delimited inventories of assigned canon files and prose chapters. Check
   requested count = parsed count, every path exists and is unique, chapter numbering has no gaps, and
   the revision still matches. Do not widen a bounded assignment.
3. **Structural checks first** (deterministic leads, not findings): chapter title/heading matrix across
   prose and packets; chapter count and act boundaries; day/timeline labels; local link targets; draft/TBD
   markers; object, technology, location and character-state tables; declared totals vs rows. Parse only
   the intended table (e.g. the leading timeline table) or numeric rows from unrelated tables will
   masquerade as mismatches. Read both sides before reporting anything.
4. **Event ledger.** One row per scene boundary: story time (bounds if vague), discourse position, POV,
   place, entities present, cause/goal, entry state, delta, exit state, evidence. See
   `references/ledger-framework.md`.
5. **State machines.** For each important entity compare chapter-entry → change event → chapter-exit →
   next-entry. Objects: holder, location, condition, last explicit transfer. Technologies: operator,
   input, preconditions, output, cost, range, known limits. Relationships: trust, obligation, authority,
   public/private status, last change event. For relationship-heavy work, list every prioritized pair
   before reading and trace each through the final chapter.
6. **Solve the timeline.** Reconcile explicit dates, durations, travel, sleep, light/weather, wounds and
   simultaneous scenes. Never turn a vague time into a precise one. Write an equation for every explicit
   arithmetic claim. Separate relative sequence proven by prose, numbered dates declared only by canon,
   elapsed-time statements in prose, and unknowns. If prose cannot decide between competing canon
   tables, report the ambiguity and the decision required.
7. **Epistemic ledger.** Track objective truth, reader evidence, each character's evidence and belief,
   confidence, channel, first acquisition, first disclosure and intended payoff. Use `unknown /
   suspected / known / false belief` (+ `withheld`), not a Boolean. Keep distinct channels distinct
   (direct perception, translation, machine overlay, telepathy, memory, inference, lie).
8. **Reader-boundary check.** Build the reader artifact from the frozen revision, map positions to
   chapters, set the position at the checkpoint and ask narrow passage queries. See
   `references/reader-knowledge-checks.md`.
9. **Compare every chapter with its packet** (whole-canon mode): material plot, title, opening/closing
   state, object/technology, knowledge, relationship and location changes, timing metadata. A packet can
   pass on plot and fail on metadata; say so. Compare promised arcs by emotional function, not literal
   quote: if prose delivers the same outcome by action, the old plan text is documentation drift.
10. **Test the mirror.** Spot-check facts across plot, chronology, objects, technology, locations and
    ending state, including at least one known stale value. A mirror that reproduces it is synchronized,
    not correct. Confirm every reported claim in the pinned files.
11. **Classify and rank.** Each finding is `NEW` (state truly changes in-story, with a cited transition)
    or `CORRECTION` (a record misstates the authority). `BLOCKER`: binding rule or required beat broken.
    `MAJOR`: impossible state, timeline, custody, knowledge, cause or relationship. `MINOR`: local
    metadata drift. `NOTE/QUERY`: intentional gap or intent-changing alternatives; ask the owner only then.
12. **Close the loop.** Cite both sides, propose the smallest complete fix, search for every duplicate and
    paraphrase, rerun the affected ledger rows and reader checkpoint, verify only intended files changed.
    Canon changes get review from someone other than the author of the change.

## Diagnostic thresholds

| Test | Pass |
|---|---|
| Citation coverage | 100% of factual findings cite both sides |
| State closure | every material entry/exit delta has an on-page or summarized transition |
| Custody | every plot-bearing object has one holder/location/condition at each boundary |
| Timeline | every explicit equation balances; every interval is physically possible |
| Knowledge | no character acts on a fact before a cited acquisition or justified inference |
| Reveal | reader/POV/public/private lanes have separate first-seen and first-understood points |
| Technology | every use respects established preconditions, capability, cost, range, failure |
| Relationship | a changed value has a scene event and persists until changed |
| Packet parity | summaries, outlines, indexes, timelines, ledgers describe the current prose |
| Retrieval | the reader-position checkpoint maps to the intended chapter |

A single incompatible value fails. If two sources describe different scopes (appearance vs reality,
belief vs truth, public vs private), split the fields before judging. Contiguous one-chapter shifts in
labels usually signal an old split or renumber: report the whole range together.

## Common failures and fixes

- **Flattening evolution:** two values at different times. Add an effective range; do not overwrite.
- **Treating a plan as prose:** mark planned vs current; update the packet.
- **Curse of later knowledge:** re-check at the earlier boundary, not by reasoning backward.
- **Search as truth:** retry exact terms, read the chapter, record `not retrieved`.
- **Private/public collapse:** add the witness set and disclosure channel.
- **Custody teleport:** find the transfer passage or file a missing-transition finding.
- **Timeline overprecision:** keep lower/upper bounds and confidence.
- **Canon overreach:** many files conflict because one root ledger is stale; fix the root, then mirrors.
- **Intent-blind repair:** a discrepancy may be a deliberate false belief or delayed reveal. Query.
- **Unbounded prose fix:** state the factual target and hand it to the author.
- **One clean grep as proof of repair:** stale canon survives as paraphrase in sheets and tables.
- **Fitting the past to a later mention:** a later line treats an event as done (an injury, a repair)
  but no scene shows it. Moving the event to another character or deleting it so the later line fits
  is not a fix; trace every later-mentioned wound or completed act back to the scene where it happens,
  and if the page shows it only started, later lines must not claim it finished.

## Implementing requested repairs (only when asked)

Turn each finding into a contradiction ledger (stale claim, authority, affected files, protected files,
acceptance search). Patch every in-scope duplicate; keep adjacent true facts. Name chronology
distinctions (event date vs duration vs date news arrived) in the replacement text. Record only
interpretive reconciliations as judgment calls. Verify with the acceptance search, stale-phrase and
paraphrase searches, `git diff --check`, and `git diff --name-only` against the write boundary.

## Output

Per finding:
```markdown
### [severity] NEW|CORRECTION — short title
- Prose/event evidence: `path:line` — exact fact.
- Canon/other evidence: `path:line` — incompatible fact.
- Ledger dimension: time|space|entity|cause|goal|custody|knowledge|relationship|technology.
- Reader/character impact: who would know or infer what, and when.
- Proposed fix: smallest authoritative change; every mirror that must follow.
- Guardrails: lock/protected/withheld/no-op status.
```
Whole-canon audits use `references/audit-report-structure.md` and must validate mechanically: coverage
ledger = assigned set exactly, every cited file exists, every line/range is within the file, revision
marker appears once, declared totals equal rows. One bad citation undermines the report. Lead with
whether prose is complete and canon safe to use; distinguish prose defects, canon defects, metadata
drift, navigation failures and mirror-sync behaviour. Say how counts were produced.

## Project specifics

The project's own skill supplies: where canon lives and which mirror/search tools exist, the binding
plan and locks, withheld lore, the entities and channels worth tracking as state machines, the reader
artifact build and its chapter mapping, review routing for canon changes, and worked examples.
