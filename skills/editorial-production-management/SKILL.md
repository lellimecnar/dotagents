---
name: editorial-production-management
description: Use when planning a book's editing/publishing pipeline. Stages, gates, routes.
metadata:
  version: "0.1.1"
  status: draft
  owner: maintainer
---

# Editorial production management

The managing-editor / production-lead role for a book: decide **which kind of edit happens when, by
whom, gated on what**, bring in outside readers at the right moment, check market fit, and know what
each publishing route requires of the manuscript. The production lead does not edit prose; they keep the
owner's decisions few and well-timed and make sure no stage undoes another. Depth:
`references/editorial-stages.md`, `references/publishing-paths.md`, `references/contradiction-triage.md`;
every claim traces to `references/sources.md` ([S#]).

## When to use

Planning or sequencing revision, editing, review or publishing work; routing work to editors, authors
and specialists; walking an owner through rulings; building a production plan from manuscript to files.

## 1. The one rule: big to small, never backwards

Each level invalidates the work of the levels below it [S1][S2][S4]:

| Level | Asks | Unit | Done when |
|---|---|---|---|
| Developmental (structural/substantive) | Does the story work? plot, arc, pacing, POV, continuity, genre fit | book, act, chapter | no revise/major verdicts left; owner's whole-book read signed off |
| Line (stylistic) | Does each sentence earn its place? voice, rhythm, clarity, repetition | paragraph, sentence | style quotas and avoid-list pass per chapter |
| Copyedit | Is it correct and consistent? grammar, usage, names, facts, style sheet | word, mark | style sheet complete; zero open queries |
| Proofread | Did typesetting break anything? typos, breaks, widows/orphans, heads, TOC | page proof (PDF/EPUB) | proof matches final copyedit; done by fresh eyes, never the copyeditor [S2] |

Tests before routing any work:
1. **Level test.** Name the level of every requested change. A developmental finding during copyedit stops
   the copyedit for that chapter; do not "just fix it" in place.
2. **Freeze test.** No line work on a chapter with an open developmental finding; no copyedit with open
   line work. Polish before revise is wasted when revision moves text.
3. **Proofs are not drafts.** After typesetting only typos and factual errors change; anything bigger
   costs a full re-proof (university presses contractually bar it [S12]).
4. **One style sheet.** The copyeditor builds and owns it (names, coinages, capitalization, numbers,
   hyphenation) [S1]; seed it from the language files and avoid-list; it is the proofreader's reference.

## 2. Readers outside the team

- **Beta readers:** target-audience readers reporting experience (what worked, what didn't, would they
  buy), not fixes [S5]. Use them **after** developmental revision and **before** line polish. Give a
  deadline, a one-page synopsis and questions keyed to known risks.
- **Sensitivity/authenticity readers:** check portrayal of lived experience the author lacks. Always
  paid; never a stamp of approval; one reader does not speak for a group [S6][S7][S8]. Engage while change
  is still possible (before copyedit) [S7].
- **Subject-matter experts:** check facts (a science reviewer can cover plausibility).
- Trigger example: any portrayal of disability, especially a plot where a disability is removed or
  "cured". Disability-in-kidlit writers name the "magical cure", "disabled saint" and "damaged/broken" tropes as harmful [S9].
  Search the prose for "broken"-type labels and recommend a paid authenticity read; the owner decides cost.

## 3. Market fit (YA as worked example; check current guidance for other shelves)

| Check | Threshold |
|---|---|
| Reader age | YA = 13+ (Kole [S10]); KDP Teen & YA band 13–17 [S16]; 11–16 straddles middle grade |
| Protagonist age | teens; Kole: 15–18 headspace [S10] |
| Length, YA fantasy/SF debut | sweet spot 65–85k; >100k "really not advisable" for querying (Laughran [S11]); Kole: avoid >100k [S10] |
| Voice | direct, honest, colloquial; dry/formal voice is the enemy [S10] |
| Standalone | a debut first book must resolve on its own |

Long length is a blocker for agents but only a cost question for self-publishing.

## 4. Publishing paths (full checklists in `references/publishing-paths.md`)

- **Agented (trade):** query letter 200–450 words (housekeeping, 150–300-word story description, comps,
  bio) [S13]; synopsis (reveals the ending); standard manuscript format (12 pt, double-spaced, 1" margins,
  title page with word count, page 1 on first text page) [S14]. Then roughly a year of in-house edits and
  production [S12][S25].
- **Small press:** same package; vet fees, distribution and contract terms; any money flowing from author
  to publisher (fees, required purchases) means vanity [S17][S18].
- **Self-publish:** you are the publisher and buy every service: copyedit (book about a month ahead;
  ~2 weeks per 80k words) and proofread [S19]; cover; one ISBN per format (US: Bowker; a retailer's free
  ISBN works only there) [S20][S21]; ebook with a working logical TOC [S22]; print PDF with trim size,
  page-count-based gutter and all fonts embedded (IngramSpark: PDF/X-1a, 0.5" margins) [S23][S24];
  retailer reading-age metadata (KDP 13–17 for Teen & YA) [S16].

## 5. Building a production plan

1. **Freeze scope decisions** (route, shelf, length, title/byline). Each open one blocks every stage below
   it; ask them all in one message.
2. **Fix the reference truth set before prose** (canon, language). Revisers read canon; stale canon
   re-breaks fixed prose.
3. **Developmental wave:** revise/major chapters in seam pairs (shared beats move together), worst-first
   only where no seam ties them.
4. **Owner whole-book read** of the revised ending; then beta and authenticity reads in parallel.
5. **Line wave** by contiguous batches, plus one cross-chapter sweep per problem class.
6. **Regenerate canon and metadata** from final prose; resync any mirrors.
7. **Copyedit** with style sheet → author query pass → final word count.
8. **Package** (front/back matter, blurb, metadata, cover brief) may start in parallel at step 5; it needs
   only a stable title, length band and synopsis.
9. **Typeset → proof → fix → re-proof → upload and approve a physical proof.**

Every stage gets an owner, an entry gate, an exit test, and a reviewer from another model family than
the worker. Effort bands are not durations: never sum S/M/L into a calendar without calibration.

## 6. Failure modes

| Failure | Sign | Fix |
|---|---|---|
| Polishing before revising | line notes on chapters with open revise verdicts | freeze test; park line work |
| "Done on paper" | logs mark fixes done that assessors still find | exit test = read/grep current prose, not the log |
| Canon drift | prose fixed against stale canon reverts | truth set first; resync after every canon merge |
| Owner as bottleneck | PRs wait days | batch asks in one numbered message; respect the owner's quiet days |
| Scope creep in copyedit | copyeditor rewrites scenes | send back to the developmental lane |
| Proof churn | text changes after typesetting | only typos/facts after typeset |
| Unvetted press | fees, "how many books will you buy?" | Writer Beware checks [S17][S18] |
| Wrong shelf | wrong ages in metadata | correct reading age and positioning |

## 7. Owner contradiction rulings

Follow `references/contradiction-triage.md`. Always: 3 items per message; check each against current
main first and drop false positives; one draft PR and one rulings log for the whole list; update canon,
docs and memory as each ruling arrives; no prose edits until every item is ruled, then one prose pass.

## Project specifics

The project's own skill supplies: who fills each lane, decisions already made (route, shelf, length,
title), open decisions, the current build pipeline and its gaps, the owner's review channel and quiet
days, where rulings logs and dossiers live, and book-specific authenticity risks.
