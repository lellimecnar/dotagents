# Setting up a queue app on a ruleset-protected branch

Use when someone asks "is the queue set up", "why does the setup banner persist", or "what do I
click". Examples use GitHub rulesets and a hosted merge-queue app because they
are common; the order of checks carries over to other forges and queue tools. To review a PR that
changes the queue rules themselves, use [queue-config-review.md](queue-config-review.md).

Read live state first (GitHub example: `gh api repos/<owner>/<repo>/rulesets`, then each ruleset by
id), then walk this list.

## Order of checks

1. **Is the app installed and reading config?** Look for the app's summary check or status comment
   on a *recent* PR; PRs older than the install show nothing.
2. **Config on the default branch.** Many queue apps read their config only from the repository's
   default branch. Read which branch that is and the config's base-branch condition: together they
   decide which branch the dashboard banner and the queue rules talk about.
3. **Require the roll-up check.** Queue conditions and the ruleset should both require the single
   aggregate CI check, not a list of jobs. A required job that is skipped or path-filtered never
   reports and can hang the queue.
4. **Bypass list.** On every ruleset that covers the queued branch or the queue's temporary branches,
   the app must be a bypass actor in the mode that fully exempts it (GitHub: *Exempt*); other modes
   leave part of the queue blocked. A token without admin rights may read the bypass list as null:
   say that instead of reporting it empty.
5. **The review requirement is still enforced.** Bypass only stops the forge from blocking the app's
   pushes; the app still treats the ruleset's required approvals as merge conditions. Read the
   required approval count and whether approval of the most recent push is required. If the count
   is one or more, someone other than the last pusher, with write access, must approve at the
   current head. When agents share one account and the review identity lacks write permission,
   those approvals do not count and every PR stalls. Surface that as the real blocker, with options:
   make the reviewer identity count (test on one PR first), lower the count to zero (the app still
   blocks on "changes requested"; review then lives in your task tracker), or approve by hand.
   - **Prove a review counts from the review itself**, not from a permission endpoint. GitHub
     example: query the PR's reviews for `authorCanPushToRepository` and the PR's `reviewDecision`
     in GraphQL; an approval counts only when the first is true and the second reads approved. The
     REST per-app permission endpoint reports `none` for apps and proves nothing. A reviewer app
     starts counting once it holds content write permission, which also lets it push code; say so
     when asking the owner to grant it.
   - **Stale reviews wake up.** When a reviewer identity starts counting, its old request-changes
     reviews on old commits start blocking too. Clear each with a fresh review at the new head by the
     same identity.
   - **Rebases cancel approvals** when last-push approval is required. After a bot rebases a PR,
     re-review at the new head before admitting, and request a rebase only once CI results are
     stale.
   - Some queue apps can post the approval themselves. That turns the forge approval into a rubber
     stamp; prefer making the real reviewer count.
6. **Restricting branch updates (optional hardening).** A setup banner may recommend that only the
   queue app may update the branch (GitHub ruleset: the "Restrict updates" rule, with the app and the
   owner or admin role on the bypass list). Save the bypass list *before* enabling the rule, or the
   app cannot merge either. Before the owner enables it, make sure the agent that admits PRs only
   labels or comments to queue and never merges directly, or its merges start failing. Without the
   restriction the queue already works; this is hardening, not a fix.
7. **Keep the app off the production branch's bypass list** when only the owner promotes to
   production.

## Doing it as code

Infrastructure-as-code providers for the forge can usually manage rulesets, including bypass actors
and the update restriction (Terraform example: `github_repository_ruleset` with a `bypass_actors`
entry of actor type `Integration`, the app's id and `bypass_mode = "exempt"`). Check whether the
repository already declares and configures that provider before saying it needs new setup. A queue
vendor's own provider, where one exists, may only toggle products and need an extra admin secret:
check what it actually manages before proposing it. Import the live ruleset, make the first plan a
no-op, and never apply without the owner's go-ahead.
