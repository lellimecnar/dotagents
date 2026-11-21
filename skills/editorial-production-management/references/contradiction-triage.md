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
   Checkers read literally and over-flag: two notes that add together ("when damaged" does not mean
   "only when damaged"), equivalent phrasings of one quantity, and states that change off-page are
   usually not contradictions.
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
   each needs and one option marked "(my pick)". Whenever a compatible reading exists (figurative,
   derived, additive, off-page change), include a "not a contradiction" option instead of passing the
   checker's verdict through as fact. Ask for an unambiguous decision on each numbered item. Offer a consolidated reply template if it suits the decision-maker’s workflow.
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
- **Apply the manuscript’s continuity rules.** Determine whether a referenced event must be shown
  on the page, may be implied, or may occur between scenes. Before accepting "it was done earlier", search the chapter
  range for the object and quote what is shown. If the text only shows a start, check whether a later completion is supported or
  allowed off-page before marking a contradiction.
- **Planning notes need record-only fixes.** When outlines, packets or indexes disagree with clear prose,
  fix the note. Classify items by manuscript impact and whether they need a decision, and route them
  according to the project’s approval and reporting rules. Agree on any standing rule before applying it to later batches.
- **Trace before logging a fix.** A fix that names an object or mechanism can create a new continuity
  error: verify the relevant state at that point against the available continuity sources, or ask
  the responsible reviewer. Record a standing rule only when one has been agreed.
- **Cross-reference related rulings.** If an existing decision settles another item, link them
  in the decision record, if used, instead of asking for a second ruling.
- **"Confirm we leave X unexplained" is not an automatic yes.** Classify each
  topic (consult the relevant reviewer if one is assigned) into needs-explaining, fine-as-is or record-only, with quoted lines and the smallest proposed
  explanation.
- **Pause affected work.** If a ruling changes active work, use the agreed hold or notification
  mechanism. Tell the responsible party what to pause and point to the new decision. Confirm the hold was received.
  If affected work has already landed, assess what must be corrected or reverted.
- **Keep status updates brief.** While decisions are pending, report only what has changed rather
  than restating every open item. If verification is interrupted, preserve completed checks and arrange an authorized
  handoff or a smaller next batch for the unchecked items.
- **Late comments.** A comment sent near a task's completion may land after the worker finished: check
  for unanswered questions and route them through the agreed follow-up channel.
