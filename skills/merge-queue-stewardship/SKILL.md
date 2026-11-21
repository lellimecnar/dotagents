---
name: merge-queue-stewardship
description: Use when admitting approved PRs to a merge queue or checking trains flowed.
metadata:
  version: "0.2.0"
  status: draft
  owner: maintainer
---

# Merge queue and release-train stewardship

How the agent that owns merges into an integration branch admits approved work, verifies what
landed, ejects a culprit, and checks once a week that work actually flowed. Also: how to review a
train PR and the CI-gate / queue configuration that decides what may merge.

## When to use

- A scheduled run admits eligible PRs into a merge queue (a queue label, a bot command) or
  assembles a release train.
- Where supported, trigger queue admission when the project’s configured review and acceptance gates pass. Otherwise use a scheduled eligibility sweep.
- A weekly check asks whether the week's approved work actually reached the integration branch.
- You review a train PR, or a PR that changes required checks or queue rules
  (see [references/queue-config-review.md](references/queue-config-review.md)).
- A queue app is installed but someone asks how to finish its setup, or its dashboard still shows a
  setup banner (see [references/queue-app-ruleset-setup.md](references/queue-app-ruleset-setup.md)).
- The queue looks stalled while checks sit queued rather than failing
  (see [references/ci-runner-capacity.md](references/ci-runner-capacity.md)).

## Principles

- **Review and admission are separate acts.** Reviewers issue verdicts; the queue steward admits;
  the queue tool batches and merges; only the owner promotes to production. Do not collapse roles.
- **Approval is bound to a head.** Approval carries across no commit. Re-read each PR immediately
  before admission and require the reviewed head to still be the current head.
- **Let the automation do its job, and prove it did.** Do not duplicate what the queue tool does
  (labels it adds itself, batching, merge commits); duplicating it hides a broken rule. Verify the
  result instead.
- **Act only under the bot's identity.** Commit, comment and open PRs as the bot account the
  profile names, never under the owner's identity or credentials: work done in the owner's name
  cannot be told apart from the owner's own. If only the owner's credential is available, stop and
  ask.
- **No eligible work means one line and stop.** Spend no further tokens on an empty run.

## Admission procedure

1. **Eligibility.** Non-draft PRs into the integration branch with: an approval from the required
   reviewer (often the other model family) *at the current head*, a QA pass where the delivery flow
   requires it, or (for bot dependency PRs) the dependency steward's final merge verdict. Exclude
   owner holds, changes requested, stale review heads, conflicting PRs.
2. **Re-read before admitting.** Base still the integration branch, head SHA equals the approved
   head, review/QA evidence still current. A changed head returns to review. Green CI is not enough:
   re-query the forge's own review decision right before admitting and require it to read approved
   at the current head. Required approvals still apply when the queue app may bypass the branch
   rules, a rebase cancels an approval under a require-last-push-approval rule, and an old
   request-changes review keeps blocking until its author posts a newer one (details in
   [references/queue-app-ruleset-setup.md](references/queue-app-ruleset-setup.md), step 5).
3. **Admit and stop.** Apply the admission signal (queue label or command) only once the admitting
   agent's standing gate passes (for example: an exact-head approval *and* green required CI). That
   the queue tool would also wait for the check does not license admitting early, and whoever
   dispatches the admission must not override a refusal or re-send the order; change the rule
   deliberately if it is wrong. Make the wait cheap instead: one bounded poll on the exact head,
   admit the moment the required check is green, read the admission signal back, and do not poll
   for the merge itself. If the wait outlasts the poll, leave the task blocked with a note of which
   jobs are green and which pending, so the next scheduled run resumes it; if the tracker can no longer
   resume it, file one fresh task that names the one it replaces. Add a priority signal
   (hotfix) only when the owner or governing policy grants it. Then stop: never build a manual train
   branch alongside a queue, merge a source PR directly, bypass branch protection, or touch the
   production branch.
4. **When the tool does not react**, read existing refresh comments and bot acknowledgements first.
   Try one representative refresh, not one per PR; an acknowledged refresh that still does not admit
   proves a systemic blocker (config not on the forge's default branch, conditions that cannot be
   met, app inactive). Hand the exact provider check to an administrator rather than merging around
   it.
5. **Verify what landed.** For each observed merge, fetch the integration branch and prove the
   frozen source head is an ancestor of it. Then confirm the deploy pipeline and health checks for
   that environment. Attribute a failure to a PR only after confirming the base tip is not also red.
6. **Eject a culprit narrowly.** If post-merge QA identifies a culprit, open a revert PR for *only*
   that PR's merge commit, put it at the front of the next batch, and give the author the evidence.
   The revert PR itself still gets a review.
7. **Report** admitted PRs, excluded PRs with the reason for each, and observed merge/deploy
   results, in one first line plus details.

## Weekly flow check

The purpose is to catch work that silently stalled between "approved" and "merged".

1. Classify every open PR into the integration branch: merged this week by the queue; queued;
   eligible but not queued; waiting on review / QA / verdict; owner hold; changes requested.
2. Bot dependency PRs older than the sweep window must each have a verdict task; create any
   missing ones idempotently.
3. Eligible-but-not-queued PRs go to the queue steward as one task listing them.
4. Anything stuck more than a few days in review or QA gets one clear next step on its task, or an
   owner escalation in the next standup.
5. If everything flowed, one line and stop. Otherwise one summary line, then details only for
   problems.

## Reviewing a train PR

A train bundles already-approved source PRs as dedicated `--no-ff` merge commits. The review is
mostly structural: prove the train holds exactly the approved bytes, the exclusions are right, and
the checks pass on the combined head.

1. **Base and parents.** Compare the integration tip with the base the train claims. For each merge
   commit, `git rev-list --parents -n1 <merge>` must show the previous train tip (or base) as first
   parent and the source PR's exact current head as second parent.
2. **Tree-identity shortcut.** If the source head already contains the base, the merge tree must
   equal the source tree (`git diff <merge> <src-head>` prints nothing; compare `^{tree}` hashes).
   Identical trees mean the train adds no content: the content review is the prior approval at that
   head, and you say so. Different trees are unreviewed conflict resolution: read the difference in
   full.
3. **Source eligibility.** For each source, a different-model approval at the exact head, its
   review task done, and a QA pass at the same head if the policy names QA.
4. **Exclusions against every open PR.** Diff the open-PR list against the train's "not included"
   table and check each row's head and reason against live state (review decision, conflicts, draft,
   hold labels, latest QA). PRs that do not target the integration branch are usually missing from
   the table; leaving them out is correct, but the table should list them or scope its heading (nit).
5. **Checks on the combined head**, re-run in one background script.
6. **Re-pin immediately before posting**: PR head and integration tip read in the same command that
   posts. Trains get rebuilt while you review.
7. A gating status that stays red until the cross-model review lands is a pre-merge confirmation
   item, not a finding.

Never merge or push to the train yourself.

## Pitfalls

- Release routines that close source PRs *unmerged* (the train merged their commits) make "closed"
  ambiguous. Prove landing with an ancestor check, not PR state.
- Keep a new admission routine active until the first verified batch *and* one failing-PR ejection
  have both happened; retire it only after telling whoever owns operations.
- Path-filtered workflows never report when not triggered, so requiring their success can hang a
  queue forever (details in the reference).
- **Prefer a rerun when the red is runner-only.** Verify that a rerun tests
  the intended head and that approvals remain valid under the configured policy.
- **Check what the CI provider reruns.** Some providers replay the original workflow and
  revision, which may differ from the current integration state. If stale workflow configuration is
  the cause, use or request the supported rebuild or requeue path rather than repeating the rerun.
- **Stale checks need a fresh merge commit.** When CI changed on the base after a PR's last run, a
  re-run can reuse the old merge commit and test stale workflow code. On a forge that builds a new
  merge commit when a PR is reopened (GitHub example: the `reopened` event, if the workflow's
  `pull_request.types` include it), close and reopen that one PR: nothing is pushed, so an approval
  under a require-last-push-approval rule stays valid, whereas updating the branch pushes a commit
  and forces a new review. Do it per PR, never as a mass sweep (that floods the runners; see
  [references/ci-runner-capacity.md](references/ci-runner-capacity.md)).
- **Land CI-infrastructure fixes first.** While a fix to the CI itself is open, every refreshed PR
  fails on the same fault; admit the fix, then refresh the rest.
- **A stalled queue may be a CI-capacity problem.** Queued (not failing) checks across many PRs
  point at runners, not queue rules. Diagnose from the forge API before restarting or cancelling
  anything ([references/ci-runner-capacity.md](references/ci-runner-capacity.md)).
- **List existing tasks before filing one.** Before filing a review, reroute or queue task for a PR,
  list the tasks already open for it: a second pass (another schedule or session) otherwise files
  duplicates that dispatch redundant reviewers and pollers. Tell a duplicate to stand down in a
  comment rather than leaving two live.
- **Check reviewer independence from the records.** Where the project's policy requires a reviewer
  independent of the author (for example from a different model family), read the author from the
  commit records or the delivery task, not from the task's default assignee or a forge identity
  several agents may share, before creating or routing the review task (see `pull-request-review`,
  "Review sweeps").

## Verification

Every admitted PR was re-read at admission time with matching head; every reported merge has an
ancestor proof; deploy health is stated per batch; excluded PRs each carry a reason.

## Project specifics

The project's own release-train skill or `AGENTS.md` supplies the integration and production branch
names, the queue tool and its labels, who may admit and who may promote, the schedule, the deploy
pipeline and health endpoints, and the tracker calls used for hand-offs.
