# Walking an owner through a contradiction list

Use this when a review or dossier pass has produced a list of contradictions (canon vs canon, canon vs
prose) that the owner must rule on.

## Procedure
1. **Find the list.** Usually a "for the owner to triage" section of a merged PR or report. Read the full
   body into a file rather than relying on a truncated tracker view.
2. **Set up one branch and one draft PR before the first ruling.** Work in a throwaway worktree from the
   main branch. Add a rulings log (e.g. `<reports>/<topic>-rulings-<date>.md`) with a Standing rules section
   and a table: item | ruling | canon/doc changes | deferred prose fix. Open the PR as a draft marked
   "do not merge until all items are resolved". Commit every later ruling to that same branch.
3. **Before showing each batch, check every item against the current main branch.** Read the cited lines
   (`git show origin/main:<file> | sed -n Np`) and grep canon for the facts involved. Lists contain false
   positives ("half a decade" vs "five years"; a long war across a region vs a short one on one planet).
   If an item is not a real conflict, say so and ask the owner to confirm; never make them rule on a
   non-conflict.
   - Look for the real problem behind the item. The surface item may be "who has the injury" while the
     real problem is that the injury is never shown happening; "the memory has no setup" may really be
     "the memory refers to a later chapter".
   - Have the authorized subject reviewer verify each issue before escalation. Keep batches within the reviewer’s demonstrated capacity.
   - Give verifiers relevant prior rulings. Check apparent contradictions against chronology, wording, context and the governing canon.
   - Route issues by subject and local decision authority. Verify evidence before escalation. Record out-of-scope items separately.
4. **Present a manageable batch in the decision-maker’s preferred format.** For each: the actual prose quoted verbatim with
   chapter:line (never a paraphrase alone; the owner should not have to go searching), a plain
   explanation of what the text says and why it may be wrong, then options A/B(/C) with the edit count
   each needs and one option marked "(my pick)". When a figurative or derived reading exists, include a
   "not a contradiction" option. Ask for an unambiguous decision on each numbered item.
5. **Each time they rule:** log the ruling and any standing rule; fix canon and doc files now (mark the item
   RESOLVED in any dossier, pointing to the log); save standing rules where the team's memory and
   guardrails live; commit and push to the shared branch.
6. **Prose stays frozen until every item is ruled.** Later rulings can change earlier ones. Collect prose
   fixes in the log's "deferred prose fix" column and route them to the author as one pass at the end,
   reviewed by the editor.
7. **Before any prose edit:** record the complete before/after text in manuscript order and obtain explicit approval from the designated decision-maker
   for the prose pass. If approval is withdrawn, stop and use the project’s authorized rollback process for any changes already applied.
8. **Reviewing canon/doc fixes:** grep by topic and by every rejected value (not only the stale phrase),
   confirm no manuscript file changed, confirm every affected canon record reflects the applicable ruling, and find every quote a canon row cites verbatim in the
   manuscript.

## Pitfalls
- When the owner gives a principle instead of picking an option, turn it into concrete proposed facts and
  ask a yes/no question on those facts before changing canon.
- Canon tables cite line numbers that may point at the wrong character's line. Re-read the cited line
  before trusting a canon row as evidence.
