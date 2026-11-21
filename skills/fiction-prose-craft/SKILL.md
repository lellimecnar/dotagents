---
name: fiction-prose-craft
description: Use when drafting or revising fiction scenes. Outside-in craft tests, fixes.
metadata:
  version: "0.1.1"
  status: draft
  owner: maintainer
---

# Fiction prose craft

The working toolkit for an agent that writes fiction: how to build a scene that turns, hold a point of
view, show rather than explain, write subtext, plant and pay off, carry worldbuilding without dumps, and
open and close chapters. It covers **how** to write. **What** is true in the story (canon, locks, arc
beats, voice profiles, house style) comes from the project's own skill and story files, and always wins
over a craft preference.

Every test below cites a craft authority; depth, quotations and worked patterns are in `references/`,
and the full bibliography is `references/sources.md`. These are diagnostic traditions, not laws. Use
them to find the problem, then use judgment about the fix.

## When to use

- Drafting a chapter or scene, or revising one after editorial notes.
- Self-checking before handing prose to an editor or reviewer.
- Skip it for canon-only, metadata or tooling work.

## Method: check outside in

Fix structure before lines. Polishing a sentence in a scene that will be cut is wasted work.

### 1. Scene function (Swain, McKee, Butcher) → `references/scene-structure.md`

For each scene between breaks, write one line: **goal → conflict → disaster** (a scene) or
**reaction → dilemma → decision** (a sequel).

- **Turn test (McKee):** name the value at stake (safety, trust, home, truth) and its charge, + or −,
  at the start and the end. If the charge does not change, the scene is a non-event: cut, merge, or add
  a real turn. Exposition alone does not justify a scene.
- **Repeat test:** no two beats in a chapter do the same job. If two beats restate the same commitment,
  keep the one that adds new information or a material cost.
- **Sequel test (Swain, Butcher):** emotional reaction → review of facts → weighing options → a choice
  that becomes the next goal. A second dilemma passage after the decision is a plateau. A decision that
  is drift, not choice, is a passive sequel (Ingermanson).
- Scenes that end on a disaster hand the next chapter its opening reaction for free.

### 2. POV and psychic distance (Gardner, Darwin) → `references/pov-and-showing.md`

Gardner's five levels run from PD1 (remote, "It was winter of the year 1853") to PD5 (inside the head).
Distance is a dial turned during a scene: closer at peaks, further for transitions and time skips.

- **Knowledge test:** every claim in narration must be something the POV character can perceive, infer
  or already knows. "No one had ever learned why" or "what he didn't know was" in close third is a viewpoint
  glitch, and it often exposits material the story wants withheld.
- **Jump test:** do not go PD1 → PD5 in one step without an anchoring sentence.
- **Head-hop test:** other characters' feelings arrive only through behaviour, speech or readouts.

### 3. Show vs tell at line level (Browne & King, Gardner, Palahniuk, Swain)

- **Named-emotion scan:** search for afraid, relieved, angry, surprised, felt, sad. A narrator-stated
  emotion is a candidate finding. Cut it; if the emotion still reads, done. If not, rewrite the beat as
  action or detail (R.U.E.: Resist the Urge to Explain).
- **Filter scan:** saw, heard, felt, noticed, watched, realized, wondered, knew. Present the thing
  directly. Keep a filter only to mark whose perception it is, or when the noticing is the point.
- **Thought-verb unpacking (Palahniuk):** "he wanted answers" becomes the detail that makes the reader
  want them. A thought verb opening a paragraph steals the thunder of what follows.
- **MRU order (Swain):** stimulus first, then reaction; within the reaction, feeling → action → speech.
  "She flinched. A shot had cracked across the yard" is backwards.
- Summary is fine for minor matters and transit. Showing everything reads breathless.

### 4. Dialogue and subtext (Hemingway, Browne & King, Turkey City) → `references/dialogue-subtext.md`

- **Countersinking:** if narration after a line or gesture explains what the reader just saw, cut it.
- **Iceberg:** the most important thing in an emotional exchange usually goes unsaid. Omit from
  knowledge, never from ignorance: know exactly what is unsaid before you hide it.
- **"As you know, Bob":** characters never tell each other what both know, for the reader's benefit.
- `said` is invisible; no said-bookisms or Tom Swifties. Action beats can replace tags. Voice rules per
  character come from the project's voice profiles.

### 5. Foreshadowing and payoff (Chekhov, Swain, Sanderson) → `references/foreshadowing-payoff.md`

- **Plant ledger:** list every object, ability or line the chapter spotlights; record its payoff
  location or mark it "texture". An unused spotlighted stimulus is a false plant (Swain).
- **Warning-stack test:** one visible defect plus one competent mitigation is a plant. Three or four
  ominous clauses announce the payoff and make the protagonist look negligent.
- **Backward grep:** every payoff needs its plant on the page earlier. Payoffs should be surprising but
  inevitable (Sanderson).
- **Sanderson's First Law:** magic or advanced tech may *solve* a problem only to the degree the reader
  already understands it. Otherwise it may complicate, cost or reveal.

### 6. Worldbuilding exposition (Walton, Turkey City) → `references/exposition-incluing.md`

- **Incluing over dumping:** scatter facts through action. A paragraph of background with no present
  action is an infodump: cut to a clause, or carry it on a prop, readout or argument.
- **Edges of ideas:** show what a technology does to people's lives, not how it works.
- **Leakage scan:** check real-world terms (place names, units, deities, brands) against canon; each is
  deliberate or a break. Avoid "call a rabbit a smeerp".
- Withheld or mystery lore is felt, never explained.

### 7. Chapter openings and endings (Edgerton, Sanderson, Kress) → `references/openings-endings.md`

- **Opening:** within roughly the first 150 words, a concrete situation and the chapter's surface
  problem, tied to the book's deeper problem. A "stealth thesis" paragraph signals what the chapter is
  about. Recap of the prior chapter ≤ one clause. No White Room openings.
- **Ending:** a disaster, decision, revelation or question that leaves the reader curious or in crisis.
  A real turn beats a cut-mid-sentence trick. No processing paragraph after the peak.
- **Mirror check:** a line deliberately echoed elsewhere keeps its wording on both sides.

### 8. Mechanical pass

Run a text analyzer (see `prose-text-analysis`) for readability, repeated n-grams and the project's
avoid-list, then apply the project's quotas. Counts locate problems; reading decides them.

## Thresholds (starting points; the project may override)

| Test | Pass |
|---|---|
| Scene turn | value charge changes in every scene |
| Repeated beats | each does a distinct job (decision, cost, threshold) |
| Narrator-named emotions | 0 unless the project licenses an exception |
| Filter verbs | each kept on purpose |
| MRU order at action peaks | 0 violations |
| Countersinking | 0 |
| Warning clauses before a planted failure | ≤ 2 |
| Opening recap | ≤ 1 clause |
| Italic direct thought | rare, by house rule |

## Common failure modes → fix

- **Plateau middle:** the same commitment restated → keep the beat that costs something, cut the rest.
- **Explained subtext:** cut the sentence after the gesture.
- **Invisible key action** (a character exits unseen, and it matters later): give one observable beat.
- **Orphan qualifier** ("Not because…" negating a reason never proposed): restore the missing beat.
- **Omniscient leak:** hedge into POV inference ("it looked like…") or cut.
- **Recap opening:** fold into action or cut to one clause.

## Verification

- Every scene has a one-line unit and a value shift recorded.
- Scans (emotion, filter, countersinking, warning stack) run over the whole chapter, not a sample.
- Plant ledger has a payoff or "texture" for every spotlighted item.
- Analyzer run and every flag read in context.

## Output format for findings

`location | test | what | why (source) | fix`. List every instance of a problem class, not one example.

## Project specifics

The project's own skill supplies: the canon and story files, exact-text locks and protected passages
(which override any craft fix: never "improve" a locked line), withheld-lore rules, character voice
profiles, POV defaults, readability targets for the audience, the avoid-list and quotas, and the
handoff workflow.
