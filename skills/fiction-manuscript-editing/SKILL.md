---
name: fiction-manuscript-editing
description: Use when editing fiction: developmental, line, copy. Staged passes, notes.
metadata:
  version: "0.1.2"
  status: draft
  owner: maintainer
---

# Fiction manuscript editing

How a professional fiction editor works a chapter or manuscript: gate the evidence, read cold, diagnose
structure, then pacing, audience and character, then lines, then copy, and write notes an author can
act on. It applies to any novel or story; the audience lens defaults to young adult because that is the
most demanding case for agency and clarity, and adapts to any readership. Sources are keyed `[S#]` in
`references/sources.md`; depth in `references/editorial-diagnostics.md` and
`references/fiction-style-and-audience.md`.

## When to use

Developmental, line, copy or combined editing; chapter assessment; editorial review of a revision PR.
Load the project's guardrails and readiness skill first: canon and binding project rules win over any
editorial preference.

## Editorial contract

- Review the exact source revision named in the task; record the revision id and file path.
- Read the owner's or author's words, outline beats, the full chapter or scene, the full diff, and any
  binding plan before judging.
- Confirm scope: structural, stylistic/line, copy, or combined [S1, S2].
- Work big to small: structure, scene, line, copy. Do not polish text likely to be cut.
- Preserve intent, voice and meaning; change only what is required [S1, S3].

## Method

### Pass 0: gate the evidence

Read repository and story guidance and any role dossier. Mark exact-text locks, withheld ("gated") lore,
protected spans, decided no-ops and completed items. Scope every affected surface: prose, canon/outline,
continuity, style sheet, invented language, technology, specialists. Reject any fix that breaks a lock,
reopens a settled decision, exposits withheld lore or exceeds scope.

### Pass 1: cold read

Read without editing. Mark attention shifts; unclear cause, motivation, location, speaker or knowledge;
scene entry/exit emotion; opening promise; ending pull. Reactions are evidence, not yet findings.

### Pass 2: developmental diagnosis

Reverse-outline every scene: entry state, goal, opposition, choice, turn, exit state, function. A scene
without a meaningful state change needs a justification beyond exposition, or it is cut, combined or
given conflict and choice [S13]. Outlines guide rather than script; deviations must be deliberate and
recorded. Then run five tests:

1. **Causality:** can each turn be "because X, the character chooses Y, therefore Z"? Flag coincidence,
   missing mechanism, effect before cause.
2. **Agency:** does the POV character's choice alter the result? Young protagonists must not become
   spectators while adults or machines solve the scene [S10, S11]. For a consequential choice, show what the character knows, what is uncertain, and why they act. If they set a condition, show how it is satisfied or why they knowingly proceed without it. A memory can inform the choice if the text establishes why the character trusts it.
3. **Knowledge:** could this POV know or infer the claim now? Separate observation, inference, memory
   and later truth. On a restricted-POV action beat, check each precise physical claim against the last
   stated sightline and what sound, touch or inference could supply. A fact the POV could not perceive
   after an explicit cutoff is a knowledge break even when automated checks find nothing; an inference
   the sensory evidence supports is fine. Separate a stated diagnosis from an inferred cause, and test the
   inference against what the reader has seen before changing it.
4. **Setup/payoff:** is decisive information planted before use without telegraphing?
5. **Continuity:** time, location, injury, inventory, names, relationships, technology, witnesses [S5, S7].
   For an object or injury correction, sweep the whole affected range, including chapters the change
   did not touch, for the bare noun and for recaps of the same event, and trace each match to its
   actual referent. Searching only for the old qualified phrase misses contradictory shorthand.

### Pass 3: pacing and balance

Label beats action, dialogue, reflection, description, exposition or transition. A **plateau candidate**
(house heuristic, not industry law) is three consecutive beats with the same function and no change in
decision, information, pressure or emotion. Flag repeated decisions, warnings, recaps and conclusions
after the beat has landed. Check action/reaction order, proportionate recovery space, an opening with
orientation plus live pressure, and an ending with changed state plus forward pull. Preserve intentional
quiet; controlled change, not speed, is the goal. (Deep pacing work: `fiction-pacing-review`.)

### Pass 4: audience and character lens

YALSA defines YA as ages 12–18 across broad reading and maturity levels [S9]; adapt for other readers.
Use readability scores as diagnostics, never pass/fail. Preserve complex ideas while clarifying sequence,
referents and stakes. Test protagonist agency, emotional immediacy, identity/relationship pressure and
owned consequences [S10]. Keep speculative vocabulary when context teaches it. Read dense lore aloud;
retain a human goal or reaction.

### Pass 5: line edit

Ask: is subject/action/cause clear? Is this the POV's register? Does the sentence add a beat? Does rhythm
fit mood [S1]? Can precision replace scaffolding without erasing voice? Sweep classes: explanatory
doubling; filter framing; abstract emotion after concrete behaviour; repeated image, conclusion, warning
or syntax; orphaned contrast; unclear pronoun; misplaced modifier; causal inversion; avoid-list or house
rule breach. Quote the first instance and list **every** other instance in scope. Context decides
whether a repeated form is a fault.

### Pass 6: fiction copy and Chicago

- Keep a style sheet: spelling, names, capitalization, compounds, numbers, italics, dialogue/thought
  conventions, technology, invented terms, POV, timeline, inventory [S1, S5, S7].
- Distinguish speech tags from action beats; physical actions do not speak words [S6].
- Em dashes for interruption; three-point ellipses for faltering or trailing speech [S6].
- Preserve the manuscript's established quotation and punctuation system unless the brief changes it.
- CMOS is a baseline, not licence to regularize purposeful fragments, dialect, rhythm or voice [S1, S7].
- Query ambiguity and intent; fix routine mechanics without burdening the author [S1, S4].

### Pass 7: actionable findings

Format: `[severity] Class — file:line — observation; reader effect; rule; fix target; all instances;
acceptance test.`

- **Blocker:** breaks causality, POV knowledge, continuity, binding rules, required function or
  comprehension.
- **Major:** scene or sequence purpose, agency, tension or emotion needs scene-level revision.
- **Minor:** bounded clarity, rhythm or copy fault.
- **Suggestion:** taste or optional strengthening; never blocks approval.

State the problem and the required outcome; offer solutions as options, not ghostwritten mandates. Notes
must be specific and reusable during revision [S12]; queries clear, tactful, succinct, actionable [S1].
Name strengths worth preserving and ask when intent is uncertain [S14]. Rank by reader harm and
revision dependency; separate required fixes from suggestions.

### Pass 8: specialists and verdict

Bring in specialists only where needed: dialogue/voice, pacing, invented language, science/technology,
continuity. Give each the exact revision, scope, the owner's words, constraints and expected output.
Fold their findings into one verdict. Approve only with no blocker and all required specialist findings
closed.

## Verification

```bash
python3 <text-analysis>/analyze.py <chapter.md> --json      # see prose-text-analysis
git diff --check
git diff --word-diff=porcelain <base>...<head> -- <chapter.md>
```
Also search explicitly for the project's avoid-list and every class you found; counts do not judge context.

## Failure modes

- **Taste as rule:** name the reader effect or downgrade to suggestion.
- **Local patch for a structural cause:** trace the earliest missing motivation or setup.
- **Over-editing:** preserve effective irregularity and author voice [S3, S8].
- **Vague note:** add location, evidence, consequence and acceptance test.
- **One instance for a pattern:** search the whole scope.
- **CMOS absolutism:** project style and fiction convention can justify exceptions [S1, S7].
- **Score gatekeeping:** use metrics to locate density, then judge the actual reader experience [S9].

## Project specifics

The project's own skill supplies: binding plan sections, locks and protected spans, withheld lore,
character-specific preservation notes, the avoid-list and style guide, the readiness rubric and finding
format, which specialist agents exist and how to route to them, and worked examples on the book.
