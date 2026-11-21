---
name: manuscript-readiness-assessment
description: Use when judging if a chapter is publish-ready. Full rubric, ranked fixes.
metadata:
  version: "0.1.1"
  status: draft
  owner: maintainer
---

# Manuscript readiness assessment

Decide whether one chapter (or a short run) of a manuscript is ready to publish, and document exactly
what work remains: a verdict, an effort band, ranked required fixes with complete line-cited instance
lists, a full rubric, and the strengths a revision must keep. Any specialist (editor, pacing, voice,
language, science, continuity) can run it; the specialty goes deepest, but the whole rubric is always
completed. Template: `references/assessment-template.md`. Report-mention ledger:
`scripts/build-chapter-mention-ledger.py`.

## When to use

Publish-readiness, editorial deep-dive, canon/outline adherence, conlang usage or revision-plan closeout
assessment of one chapter. Not for drafting or revising (use the drafting/editing skills), and not for a
whole-book canon audit (use `story-continuity-audit`).

## Always-on rules

- **Read-only by default.** No manuscript or canon edits, branches, commits, fixes or delegated fix
  tickets unless the task explicitly authorizes them. Scratch extracts and drafts live outside the repo.
- **Problems are findings**, not unfinished work. Finish the assessment and use the requested terminal
  disposition even when the verdict is adverse.
- **Classes, not examples.** One ranked finding per problem class, listing every clear instance with
  `file:line-range`. Give a concrete operation (cut, move, combine, clarify, preserve), never "tighten".
- **Required vs suggestion.** Rank required fixes separately from taste. `unknown` must name the missing
  source or unresolved conflict; never use it to dodge a supported judgment.
- **Frozen source is authority.** Use search mirrors and indexes for discovery; confirm every material
  claim against files at the requested revision. Prior reports are leads, not authority.
- No secrets or connection strings in documents or comments.

## Procedure

### 1. Freeze the task contract

Read the latest direction before any write: exact revision, write policy, rubric/template, destination,
summary shape, status and stage rules. The latest explicit destination wins; if it redirects the
deliverable or forbids some mutations, skip every superseded write even if an older template asks for it.
Check out the full revision detached, verify `git rev-parse HEAD`, confirm a clean worktree, and record
the short revision in the assessment exactly as requested.

### 2. Read the governing rubric first

Copy its verdict meanings, effort bands and required rows into your skeleton. Assign verdict and effort
by **edit geometry**, not citation count:

- `ready`: nothing beyond taste.
- `polish` / S: line and copy fixes in one pass.
- `revise` / M: scene-level changes in a few places; one or two passes.
- `rework` / L: missing beats, structural change, or unresolved owner decisions.

A repeated line-level class can still be `polish`/S when one focused pass fixes every instance.

### 3. Build the evidence set

Read the full chapter, its outline and notes, act-level continuity, and the canon pages for every
character, location, technology, language term, count and timeline fact that could affect the verdict.
When the task requires covering every prior report that mentions the chapter, build a deterministic
mention ledger instead of trusting one broad grep:

```bash
python scripts/build-chapter-mention-ledger.py --reports-root <reports-dir> \
  --chapter 9 --title "<chapter title>" --output "$TMPDIR/ch09-report-ledger.md"
```

Take file and hit totals from the script output, page through the whole ledger, open full source
sections that could affect the verdict, and keep two outputs: a coverage list of every matching file and
a disposition table for every substantive chapter item (`open`, `resolved`, `superseded`, `inventory/
context only`). Use word-bounded chapter patterns (an unbounded `ch9` matches other numbers). Cite the
original report and current source, never the extracted ledger.

### 4. Reconcile reports against current source

Read closeout records and current plans before older audits.
- Verify every `DONE` / `clean` / `no change needed` claim against the frozen source. If the phrase,
  quota breach, POV slip or contradiction survives, it is a live finding; cite both the occurrence and
  the failed closeout claim.
- When a report still says "pending" but the manuscript already has the change, that is record drift,
  not a prose defect; require status reconciliation so a later pass does not redo or revert it.
- Drop historical findings the current text resolves; do not keep stale severity.
- When paths or line anchors drifted, locate the claim by quoted text and cite the current `file:line`.
- When authority files conflict, look for a later explicit ruling; use it for fix direction but still
  list every contradictory source as synchronization drift.
- When fresh independent reads are part of the evidence, read each reader's raw findings at the exact
  revision, not only scores or a coordinator's convergence summary. Adjudicate findings only one reader
  raised, too: an isolated top-severity finding can expose a separate decision beat, while its proposed
  remedy may exceed scope or reopen a ruling. Record the remedy you accept. A score from a reader who saw
  only an excerpt is provisional, never a chapter score.

### 5. Map scenes and beats before judging

Divide the chapter into contiguous line ranges; compute approximate words per segment with a script.
For each segment record function, dominant mode, tension and transition. Trace every occurrence of a
load-bearing prop, gesture, countdown or motif that carries the opening or closing payoff: a duplicated
state change earlier can spend the climax or create a custody contradiction. Name the denominator and
label shares approximate.

### 6. Review the whole rubric

Prose/copy, voice/relationships, pacing/structure, continuity, invented language, science/technology/
world rules, outline adherence, open plan items. Check the opening, scene turns and the final pull, not
just sentences. Run text statistics (`prose-text-analysis`) only after the cold read, and read every
flagged instance in context before promoting it. For conlang and gated terms use `conlang-usage-review`.

### 7. Write findings

For each class: the publication problem; every in-scope occurrence; the mechanism (where momentum
stalls, a beat rushes, tension resets, closure repeats, exposition pre-announces an emotion, continuity
breaks); acceptable character belief or dialogue vs narrator/canon assertion; the governing canon or
report item; the exact form or a bounded revision operation; a preserve list and, where evidence
supports it, a cut target. Keep overlapping evidence under one finding and cross-reference it from
several rubric rows. Separate prose defects from stale supporting metadata; group all stale-record
instances in one synchronization finding; never weaken deliberate later escalation to match old plans.

### 8. Verdict, draft, verify, deliver

Rank by publication impact. Use `ready` only when no required prose or continuity work remains. Before
delivery verify: revision still matches, worktree still clean, every declared total computed (not
typed), each required fix has current evidence and a concrete correction, every rubric row has a status
and note, the source label appears in the document. Deliver in dependency order: write and read back the
document; then post and read back the verdict comment (verdict plus top three fixes); only then set the
terminal status and read it back. Closing first can conceal an incomplete delivery. Honour any stage
hold: finishing an assessment does not advance the chapter.

## Readiness pipelines (when a team runs this per chapter)

A common chain: **Assess** (read-only, verdict/effort/source in structured fields) → **Revise** (one open
revision PR per chapter, with notes on deliberate outline deviations) → **Editorial review** (required
fixes vs suggestions; specialists pulled in only where needed and folded into one verdict) → **Canon
check** (one canon PR naming the revision PR; contradictions spawn a new revise + check) → **Owner
review**. Rules that keep it sane: never open a competing PR; the newest stage document of a kind wins;
reviews come from a different model family than the work; canon mirrors update only after merge.

When independent cold reads are part of approval:
- Re-score if a revision changes a scored passage or any context that could change its assessment. Reuse earlier scores only after checking both; record the revision and the basis for reuse.
- Resolve required findings before approval even if all scores meet their thresholds.
- Keep readers blind (no model names, drafters, history or earlier scores), and spot-check each packet:
  complete target text, no authorship terms. If a required reader route is unavailable, stop with the
  exact error; never substitute another.
- A reader's "the rule causes this" label is not a decision for the owner by itself: if a fix exists
  inside the rule, it is a required fix. Record deliberate gaps the owner already ruled on as closed, so
  later readers stop flagging them.
- If a beat still scores low after the within-rule fix has landed and the remaining levers would change
  the design, it is the owner's decision: offer the smallest design change. Another wording round on a
  design problem only burns a review cycle.
- Hold revisions that depend on an unresolved design decision. Continue independent fixes that will remain valid either way. Batch the dependent fixes after the ruling and recheck the affected passages together.

## Pitfalls

- Treating a prior "done" log as proof; read the current prose.
- Copying an old finding without checking the pinned text.
- Converting a count straight into a finding.
- Padding a class with borderline examples; preserve successful exceptions a broad fix could erase.
- Describing a fallback copy as equivalent to the required destination.

## Project specifics

The project's own skill supplies: the rubric file and verdict scale, chapter status and holds, report
locations, the stage chain and who owns each stage, tracker/card conventions, canon sync procedure, and
specialist routing.
