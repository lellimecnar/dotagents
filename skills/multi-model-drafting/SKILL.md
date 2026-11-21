---
name: multi-model-drafting
description: Use when drafting prose with several models. Fan out, merge.
metadata:
  version: "0.1.2"
  status: draft
  owner: maintainer
---

# Multi-model drafting

Several models write the same passage from the same brief; one agent then merges the strongest parts into a
single version. Different model families make different mistakes and have different strengths, so a merge
of a few good drafts tends to beat the best single draft. In one blind test (two scenes, two judges from
different model families), the best merges outscored every solo draft by about a quarter.

## When to use

- New prose, or a full rewrite, where quality matters more than speed or cost: chapters, scenes, key passages.
- Not for line edits, quick fixes or factual updates. One careful pass is cheaper and just as good there. A
  project may still opt in for review fixes on passages that matter; see "Revising with several models".

## Choosing the drafters

- Choose by measured results on your own material, not by reputation. A model that is strong elsewhere can be
  weak at a particular voice, and an open-weights model can beat the frontier models on it.
- Prefer variety across model families over more drafts. Each extra drafter costs time and gives the merger
  more to read. In the test, three or four good drafters scored the same, and the drafters that broke canon
  often added noise rather than material.
- Drop a drafter that repeatedly breaks hard constraints (canon, point of view, locked facts), or that takes
  far longer than the others for no gain in quality.

## Fanning out

1. Write one brief first, complete enough that a writer with no other context could draft from it (see
   `layered-chapter-drafting`, Phase 0). The drafters cannot look anything up, so whatever they need must be
   in what you send them: the brief, the voice and prose rules, and any canon the scene depends on.
2. Send every drafter the same goal and the same context. Before you dispatch, check that each task really
   carries the full text, not a placeholder or a reference to a file the drafter can't read. In the test,
   one drafter got a placeholder and wrote a plausible draft from nothing.
3. Ask each drafter for finished prose at the target length, saved where you can read it, and nothing else.
   Have them write to files rather than return the text, with a clear marker at the start of each unit
   (scene, passage): the drafts then stay out of your context until you open them, and the
   markers let you line up the same unit across drafts.

## Merging

1. **Read blind.** Strip anything that says which model wrote which draft before you compare them. A merger
   tends to prefer drafts written by its own model family. In the test, the merger used a draft from its
   own family as the spine six times out of eight, though both judges ranked another model's drafts higher.
   If the merger has already seen the model labels, use an authorized independent evaluator who
   receives only the common brief and shuffled, anonymized drafts. Use the ranking to inform step 3
   only after ruling out drafts that fail the hard constraints in step 2.
2. **Check every draft against canon and the rules before judging style.** A beautiful draft with a canon
   break is not the spine. Note each draft's errors so none of them carry into the merge.
3. **Pick the spine:** the draft with the best structure and the fewest hard errors. Then bring in specific
   passages, beats and lines from the others, each for a reason you can name (a sharper image, a better
   line of dialogue, a missing beat).
4. **Make it one voice.** Rewrite the joins so nothing reads as stitched together: no shifts in voice,
   repeated beats or details that contradict each other.
5. **Write merge notes** next to the result: which draft was the spine and why; each passage taken from
   another draft; each error fixed; anything rejected on purpose. Reviewers use them, and they show over
   time which drafters earn their place.

## After the merge

The merged version is a draft, not a final. Run it through the usual accuracy gate and review. In the test,
the top merges still needed one to three line fixes, and one carried a canon break that no single draft
had: merging can create errors as well as remove them.

## Revising with several models

Use only where the project opts in for review fixes; otherwise one careful pass is enough (see "When to use").

- Give every model the same narrow task per finding: only the affected range with a little context either
  side, the issue, the facts it must keep, and the proposed fix as one option among others. Ask for the
  revised range only, so the picks can be spliced back without touching the rest.
- Short fixes often come back identical, or nearly, from every model. Report only the contested picks; an
  uncontested "winner" is just the tiebreak and says nothing about the drafters.
- When separately judged passages depend on each other (a detail planted in one and paid off in another),
  fix the shared content in the brief, so picks taken from different drafts still join up.
- Copy the reviewer's keep list (lines that must survive) into every unit brief word for word. A keep
  softened to "may stay" lets the winning draft drop it, and restoring it costs another full round.
- Keep the shuffle key hidden from the judges until the picks are applied (see "Read blind" and
  "Measuring a drafter set").

## Auditing a multi-model pass

When you review work that claims it went through draft-and-judge:

- Match every changed passage to an actual anonymous draft unit, not to the run's aggregate tally, and check
  the selected text against the committed change. A fix applied before the blind round (even a number-only
  correction) has not been through it.
- Recompute rankings and ties across every candidate before reading the shuffle key: the judges' first
  choices alone can hide a third tied candidate. Confirm the recorded winner, and judge the winning text
  yourself.
- Exact ties in rank sum and score are recorded as ties, with the direction the fixed tiebreak fell,
  especially when it favours the coordinator's own model family.
- Identical drafts prove there was no textual choice, not that the judges ran. Check every unit, follow-up
  rounds included, against the judge records, and report any unit that skipped judging.

## Measuring a drafter set

When you need to choose or re-check the drafters:

- Use two or more real passages of different kinds (for example dialogue-heavy and action-heavy).
- Judge blind: shuffle and relabel the drafts, and keep the key where the judges can't see it.
- Use at least two judges from different model families, scoring the same criteria (voice, fidelity to
  the brief, craft, character and dialogue, pacing), listing fatal flaws, and naming the passages worth keeping.
- Compare the merges against the best solo draft and against your current single drafter, judged blind
  in the same round.

## Project specifics

The project's own skill supplies: which models draft and which merges, how to dispatch drafters in its
tools, where briefs, drafts and merge notes live, and the voice and prose rules every drafter receives.
