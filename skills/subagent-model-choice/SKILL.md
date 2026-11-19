---
name: subagent-model-choice
description: Use when delegating a subtask. Pick the cheapest model that can do it well.
metadata:
  version: "1.0.0"
  status: active
  owner: Lance
---

# Choosing a model for a subagent

When you delegate, the delegate tool may offer named model options (for example `fast`, `standard`, `deep-claude`,
`deep-codex`). Pick per subtask, deliberately. The aim is the right capability at the lowest cost to shared usage
limits, not the most powerful model available.

## How to choose
1. **fast**: the default for small, well-defined work with a checkable result: lookups, extraction, summaries,
   formatting, simple edits, routine checks, searching files or the web.
2. **standard** (your own model): the subtask needs the same judgment you would apply yourself, or fast already
   failed on it once.
3. **deep** (the most capable, most expensive models): rare. Use only when at least one is true:
   - The problem is genuinely hard: architecture or design with lasting consequences, subtle multi-step reasoning,
     a bug that standard could not find.
   - A wrong answer is costly or hard to undo: money, legal, medical, safety, irreversible changes.
   - standard has already failed on this subtask twice.
   Pick the deep option whose strengths match the work (the option descriptions say which). Write the reason in the
   subtask goal in one sentence, e.g. "deep: standard failed twice to find the race condition".

## Rules
- Volume, length or importance to the user alone do not justify deep. Split long work into fast or standard pieces.
- Never use deep for routine or recurring work (scheduled jobs, triage, status checks, summaries).
- If unsure between two options, choose the cheaper one and escalate only if the result falls short.
- If no model options are offered, the subagent runs on your model; do not work around that.
