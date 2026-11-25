# Adversarial review of a plan

Use when the requester asks for a plan to be interrogated claim by claim, or when a plan will commit large effort
or an irreversible choice. Assign complementary lenses to the panel to check different failure modes,
then use the claim registers to see what was covered and where gaps remain.

## Set-up
1. Gate every panel task on one pinned revision of the plan (one commit or document version), so all lenses review
   the same text. Each reviewer records the revision it reviewed.
2. Write one shared contract that every reviewer reads first: the requester's verbatim words, the revision, the
   lens list below, the register format, the severity scale and what counts as evidence.
3. Run the lenses in parallel. Reviewers never edit the plan or shared inputs; checks they run (setup, acquisition)
   run in their own scratch checkout. Typical lenses (merge or drop to fit the plan):
   - **Facts:** every fact, number, pin and citation, re-checked at the source (re-hash, re-run), plus any decision
     gate that passes by construction or on missing or wrong-scope evidence.
   - **Assumptions:** every stated or silent assumption: what breaks if it is false, and would the plan notice;
     up to three cheaper or better methods on data already held, each with a cost.
   - **Practicality:** run the setup and acquisition steps verbatim, flag every guess, check the estimates, name
     cuts where cost exceeds the value of the decision, and check that off-the-shelf options were used.
   - **Domain:** every domain fact and every frozen expected answer, checked by someone who knows the field.
   - **Intent:** every claim about what the requester wants and every recorded decision cited; would the report
     template mislead a non-expert reader.
   Use reviewers on more than one model family where you can. Have each state its model in its result and check
   those lines against the dispatch records before calling the review multi-model: a lens that silently ran on
   the default model is the usual gap.

## Claim registers
Each reviewer writes a register: one row per claim with the verdict, the evidence and a severity, and a stated
denominator (claims checked / claims in its lens). "No findings" without a denominator proves nothing.

## Revision
1. One revision task for the plan's author, after all panel tasks. The author writes a review ledger marking every
   finding fixed (where), rejected (with evidence) or known limit. No critical or high finding stays open.
2. The author revises, re-runs authorized setup steps verbatim, and re-costs the plan (practicality cuts
   are adopted unless rejected on evidence). Reviewers for the affected lenses verify the ledger entries
   for their findings and re-check changed claims and any claims or decision gates those changes affect.
3. The final approval reviews the revised revision, not the one the panel saw.
