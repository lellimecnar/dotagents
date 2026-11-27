---
name: git-and-github-workflow
description: Use when writing to git/GitHub. Branch, commit, PR, review.
metadata:
  version: "0.1.0"
  status: draft
  owner: maintainer
---

# Git and GitHub workflow standards

How an agent uses git and GitHub so that its work is easy to review, safe to integrate, easy to
revert and readable a year later by someone who never saw the task. It covers the author's side of
the whole lifecycle: branch, commit, shape history, keep up with the base, open and describe a pull
request, answer review, and clean up. Exact commands are in
[references/commands.md](references/commands.md).

Related skills own the neighbouring jobs, and this one links to them rather than restating them:
`coding-task-intake` (the task loop: intake, build, verify, report), `engineering-principles` and its
`references/review-protocol.md` (what a review finding is and how a reviewer posts one),
`pull-request-review` (verifying another agent's PR), `merge-queue-stewardship` (admitting and
merging), and the bundled `github` skill (CLI mechanics). Where the bundled skill's examples differ
from this standard (branch prefixes, "merge when green", squash by default), this standard and the
repository's own rules win; use the bundled skill for command syntax only.

## What these standards protect

| Value | What it means here |
| --- | --- |
| **Reviewability** | A reviewer can understand the change, and each later round of it, without reconstructing your session. |
| **Revertability** | Any change can be backed out cleanly, alone, without dragging unrelated work with it. |
| **Traceability** | Every line on the integration branch leads back to why it exists: a commit message, a PR, a request. |
| **Shared-state safety** | Nothing you do destroys or rewrites work that another person or agent relies on. |
| **Low noise** | Branch lists, PR timelines and notifications carry signal, so the people who read them keep reading them. |

These are principles, not a checklist. Each section says what it protects and when it does not
apply; when a case is not covered, decide by the value at stake and say in the PR what you chose.

## 1. The repository's own rules come first

Every repository already encodes many of these decisions: its `AGENTS.md`/`CLAUDE.md`,
`CONTRIBUTING.md`, PR template, commit-lint config, hooks, branch rulesets, merge-queue config and
the merge methods it allows. Those are binding project rules; this skill fills the gaps between
them and explains the reasons behind them. When a project rule and this skill disagree, follow the
project rule and, if it looks wrong, raise it rather than working around it.

Read the live settings before your first write in a repository, because they change how every
later step should go, and documents about them drift:

- **The integration branch** you branch from and target (it is not always the default branch, and
  instructions sometimes name a branch that is frozen or reserved for releases).
- **The merge methods allowed** (merge commit, squash, rebase). This decides how much your commit
  series matters (section 4).
- **Rules on the target branch:** required reviews, whether a push dismisses approvals or requires a
  fresh approval of the last push, required conversation resolution, required status checks and
  whether the branch must be up to date, linear history, a merge queue.
- **Hooks and lint configs** that will run on your commits.

The commands are in the reference. If you cannot read a setting (no permission), infer it from the
repository's history and docs, and say in the PR what you assumed.

## 2. Branches

**When to create one.** One branch per reviewable unit of work: one concern, one PR. Create it from
the freshly fetched integration branch, never from whatever happens to be checked out.

- To fix or extend an open PR, work on that PR's branch: a second PR for the same goal splits the
  review and the history. Check your open PRs and remote branches for the goal before you start.
- Separate concerns get separate branches even when you notice them mid-task (an unrelated bug, a
  refactor the feature does not need). Finish or park the first branch cleanly before switching.
- Branch from another feature branch only when you are deliberately stacking dependent changes
  (section 6), and say so in both PRs.
- Never commit on the integration branch, the default branch, a release branch, a shared clone that
  others use as a base, or another author's branch, unless that branch's owner asked you to and the
  repository allows it.

**Naming.** `<type>/<short-description>`, following the Conventional Branch convention, with the
same type vocabulary as the repository's commit types:

- `type` is the kind of change: `feat`, `fix`, `docs`, `refactor`, `test`, `perf`, `build`, `ci`,
  `chore`, `revert`, plus `hotfix` and `release` where the repository uses them.
- `short-description` is lowercase letters, digits, hyphens (and dots for versions): three to six
  words naming the change, not the activity. `fix/search-empty-query-crash`, not
  `fix/bug`, `fix/stuff-v2` or `update`.
- Add the tracker key when the repository or tracker links on it (`feat/abc-123-export-csv`).
- Add an identity prefix only where the project or your instructions define one (some teams prefix
  the author agent or role so ownership is visible in the branch list); otherwise leave it out.
- Not a bare date or ticket number, not a generated name, and never the name of an existing tag
  (a branch and a tag with one name make every command that takes a ref ambiguous).

If your workspace tool created the local branch with a generated name, and cleans it up by that
name later, keep the local name and push under the convention name (`git push -u origin
HEAD:<type>/<short-description>`). The remote branch, which is what everyone else sees, then
follows the standard without breaking the tool's cleanup.

**Lifetime.** Short. A long-lived branch diverges from its base and turns review and merge into
archaeology. Integrate in small PRs instead of keeping a branch open for weeks. After the PR merges
or closes, delete the branch you created (unless the repository deletes merged branches for you)
and remove its worktree. Never delete a branch or tag you did not create.

## 3. Commits

**One logical change per commit.** Each commit should be explainable in one sentence and, where its
commits will land on the integration branch, should build and pass the tests on its own, so
`git bisect` and `git revert` work on it. Keep these apart, each in its own commit:

- a refactor or move, and the behaviour change that follows it (a reviewer can then check the
  refactor changes nothing);
- formatting or generated-file churn, and real edits;
- a dependency or lockfile change, and the code that uses it, when the repository reviews them
  separately.

*When not:* a few-line fix with its test is one commit. Splitting below a meaningful unit adds noise.

**Messages.** Follow the repository's convention; most use Conventional Commits, and commit-lint
enforces it where configured (read its config for the allowed types and scopes instead of
guessing).

```text
<type>(<scope>): <imperative summary, no trailing period>

<Why the change is needed: the problem, the constraint, the decision.
What changed at a level the diff does not show at a glance. Wrap
around 72 columns.>

<Footers: BREAKING CHANGE: ..., Refs: #123, Co-authored-by: ...>
```

- The subject says what the commit does when applied ("fix(search): reject empty queries before
  hitting the index"). Keep it short enough to read in a one-line log (about 50 characters is a good
  target; respect the repository's limit).
- The body explains *why*, because the diff already shows *what*. Write it for a reader who has
  neither the task nor your session. Leave it out only when the subject says everything.
- Do not narrate the process ("tried X, then Y"), praise the change, or paste tool banners or
  generated attribution lines the repository does not ask for.
- Mark breaking changes the way the convention does (`!` after the type and a `BREAKING CHANGE:`
  footer), so release tooling and readers see them.
- Commit as the identity your environment configures. Never change the git identity or commit as
  someone else: the history is the record of who did what.

**Hygiene before every commit.** Review the staged diff, not just the file list; stage paths
deliberately (never a blanket add of a tree you have not read); keep build output, local env
files, editor files and large binaries out unless the repository expects them; scan the staged diff
for secrets and personal data. Never skip hooks (`--no-verify`): a failing hook is a real failure
to fix. If a secret is ever committed, treat it as leaked even if you rewrite it away, and report
it so it is rotated: pushed history has copies you cannot reach.

## 4. Shaping history

What deserves effort depends on what will land on the integration branch, which the merge method
decides (section 1):

- **Merge commits or rebase merging:** every commit you push lands as it is. Before you ask for
  review, make the series read as a clean sequence of logical steps: fold fixups into the commit
  they fix, drop "wip", "typo", "address review" and "fix lint" commits, and reorder so each commit
  builds on the previous one. A PR whose series is ten commits of trial and error puts that trial
  and error on the integration branch forever.
- **Squash merging:** the PR title and description become the commit. The individual commits matter
  far less; spend the effort on a PR title that is a valid commit subject and a description that is
  a good commit body. Do not churn the branch rewriting commits that will be squashed.

Mechanics that keep shaping cheap:

- While working, commit small and often. Record a correction to an earlier commit as a fixup
  commit aimed at it (`git commit --fixup=<sha>`), then fold them all in with an autosquash rebase
  when you are ready for review.
- After any history rewrite, compare the old and new series with `git range-diff` and read it: it
  proves the rewrite changed what you intended and nothing else, and it is the view a reviewer needs
  to re-review a rewritten branch.
- Where pushing dismisses approvals or requires approval of the last push, every push starts a new
  review round. Do your history clean-up in the same push that answers a review, before you
  re-request review, and never after an approval.

*When not:* never rewrite history that is not yours alone (section 5). A merged commit is never
rewritten; it is reverted.

## 5. Keeping up with the base: rebase or merge

**Rebase** your branch onto the fresh integration branch when the branch is yours alone: the
history stays linear and each commit resolves its own conflicts. **Merge** the base into your branch
instead when others have pushed to it or have branches built on it, when the repository says to,
or when a rebase would silently change commits a reviewer already approved and the repository does
not allow re-review. Never rebase, reset or force-push the integration branch, the default branch,
a release branch or anyone else's branch.

Update only when there is a reason, because each update re-runs CI, notifies watchers and, under
last-push approval rules, sends the PR back for review:

- the PR conflicts with the base;
- the target branch requires branches to be up to date;
- a change on the base affects your code (an API you call changed, CI was fixed in a way your
  checks need);
- your PR has not been tested against a base that has moved a long way.

Not because the base gained unrelated commits. Merge queues test the combined result for you.

**Force-pushing** a rewritten branch of your own: only with a lease that names the head you expect
to replace (`--force-with-lease=<branch>:<sha>`, where the SHA is the head you last pushed, which is
the PR's current head), never a plain `--force`. The lease refuses the push if anyone else's commits
reached the branch since, so you never overwrite them. When the local and remote branch names match,
`--force-with-lease --force-if-includes` is an equivalent shortcut. If the PR already has review
activity, leave a short comment that the branch was rewritten, with the old and new head and the
`git range-diff` command, so the reviewer can re-review only what changed.

**Conflicts.** Resolve hunk by hunk, keeping the intent of both sides; never take one whole side of
a file to make a conflict go away, because that silently drops the other side's unrelated changes.
Afterwards re-run the checks, and read the range-diff or the merge result before pushing.

## 6. When to open a pull request

Open a PR when there is something a reviewer can act on, or earlier as a **draft** when visibility
helps (phased work that should survive a restart, an approach you want checked early, work that
others depend on). A draft is a progress marker, not a resting place: mark it ready when the work
is complete and verified, not when it merely runs.

- **One concern per PR.** A reviewer should be able to give one verdict on all of it. Mixed PRs get
  shallow reviews and cannot be reverted cleanly.
- **Size for one sitting.** Split large work into a sequence of PRs that each leave the integration
  branch working (a refactor first, then the feature on top; scaffolding behind a flag, then wiring).
  Size is judged by how hard the change is to review, not by a line count; a large mechanical change
  (a rename across files) can be one PR when it is one obvious transformation.
- **Stacked PRs** (a child PR based on its parent's branch) when a later step genuinely needs an
  earlier one that is still in review. Say so at the top of both descriptions. When the parent
  merges, retarget the child to the integration branch and rebase it onto that branch so only its
  own commits remain; GitHub does not do this for you in every case, and a branch cannot be deleted
  while an open PR still uses it as a base.
- **Before opening,** search open PRs and branches for the same goal; extend an existing one rather
  than opening a competitor. Do not open a PR with no change, and do not open one against a
  production or release branch unless your role owns promotions.
- **Target** the integration branch from section 1, and confirm the base in the created PR.

## 7. The PR title and description

The description is the record of the change: reviewers read it first, a squash merge turns it into
the commit, and whoever investigates a regression later reads it to learn why. Use the repository's
PR template when there is one. Otherwise:

- **Title:** a commit-style subject for what lands (`feat(export): add CSV export for reports`), in
  the repository's commit format when it squashes or lints titles. Not "Fixes", not a task id alone.
- **Why:** the problem or request in two to four lines, linked to its source (issue, spec, decision
  record). Quote the requester when their words decide what "done" means.
- **What changed:** grouped by area, at the level a reviewer needs to navigate the diff; call out
  anything surprising, deleted, renamed or deliberately left alone.
- **How it was verified:** each check you ran with its result, and what you looked at (screenshots
  for anything a user sees). Link CI rather than pasting logs.
- **Risk and rollback:** what could break, who or what is affected, how to back it out (usually
  "revert this PR"; say if data or config changes make it harder).
- **Decisions to confirm** for the reviewer, lettered, each with your default.
- **Blockers**, only genuine ones, with evidence (an owner-only action, an outside dependency, a
  hard limit); leave the section out when there are none.
- **Links:** `Closes #123` only closes the issue when the PR targets the repository's default
  branch; GitHub ignores closing keywords on PRs into any other branch. For other bases write
  `Refs #123` and close the issue yourself after the merge reaches where the issue counts as done.
  Name dependencies on other PRs ("Depends on #456").

Keep it true. When scope or approach changes during review, edit the description in the same round:
a stale description misleads the reviewer and, under squash merging, the history. Write the body
from a file, never from inline-quoted markdown. No secrets, internal credentials or personal data,
remembering that public repositories publish everything in a PR, including its comments.

Use reviewers, labels and assignees the way the repository does: request review through the
platform's review request so it is tracked, and do not apply labels that trigger actions your role
does not own (merge-queue admission, release labels).

## 8. Comments and review

GitHub has three kinds of comment, and each has a job:

- **Review comments** (inline, on diff lines, grouped into one review with a verdict) carry
  findings. A reviewer posts the whole review at once, pinned to the head commit it covers, as
  `engineering-principles` → `references/review-protocol.md` describes.
- **Review verdicts:** *Approve* means "this head may merge"; *Request changes* means a blocking
  finding is open; *Comment* is for questions and non-blocking notes. Never approve your own work,
  and post verdicts as the identity your instructions name.
- **Conversation comments** (the PR timeline) carry things about the PR as a whole: a summary of a
  review round, a rewrite notice, a status the next reader needs. Not a running log of your session:
  every comment notifies everyone watching.

**Answering a review (author).**

1. Read every finding before changing anything, and fix each as a class, per
   `engineering-principles` → `references/fix-protocol.md`.
2. Reply in each review thread: what you changed and the commit that changed it, or why you
   disagree, with evidence. A silent push leaves the reviewer to diff and guess. One reply per
   thread; replies go on the thread's first comment.
3. Leave resolving a thread to whoever opened it, unless the repository's convention gives that to
   the author. Resolving someone else's concern for them hides it from them.
4. Apply small suggested changes as a batch in one commit rather than one commit per suggestion,
   especially where commits land as they are.
5. Edit the description if scope changed, push once, then re-request review from the same reviewer
   so the request is tracked. One summary comment per round is plenty.

**As a reviewer,** use suggested-change blocks for small concrete edits the author can apply in one
click, keep findings in review threads (not timeline comments) so they can be tracked and resolved,
and resolve your own threads when the new head addresses them. Dismissing someone else's review is
an act with an audit trail: only when your role allows it, and always with the reason.

## 9. Merge and after

Merge only if your role is the one that merges, by the method the repository allows, at the head
that was approved; never bypass branch protection or a merge queue. Otherwise stop at a ready,
approved PR and hand it to whoever merges.

After a merge you were part of: confirm it landed (the reviewed head is an ancestor of the
integration branch, not just "PR closed"); delete your branch and worktree; close or update issues
the closing keywords did not handle; and start whatever the work's last step is (deploy check,
follow-up PR). To undo a merged change, open a revert PR (`git revert`, with `-m 1` for a merge
commit) and give it a review like any other; never rewrite the integration branch to remove it.

Tags and releases belong to whoever owns releases. If that is you: annotated tags, created by the
repository's release tooling when it has one, and never move or delete a published tag, because
other people's builds and caches already point at it.

## Pitfalls

- A branch that merges the base into itself again and again, under a merge-commit policy, puts each
  of those merge commits on the integration branch. Update by rebase when the branch is yours, and
  only when section 5 gives a reason.
- A bare `--force-with-lease` with no expected value overwrites someone else's commits when a fetch
  (yours, or a tool's in the background) already updated your remote-tracking ref. Name the expected
  SHA. `--force-if-includes` covers the same gap but refuses wrongly when you push under a different
  remote name than your local branch.
- A closing keyword in a PR into a non-default branch does nothing, so the issue silently stays open
  or is believed closed. Check the base.
- Branch auto-deletion can be skipped while another PR is based on the branch; stacked children must
  be retargeted, or they end up showing their parent's commits again.
- A rewrite pushed after approval, under last-push approval rules, discards that approval. Clean up
  before review, not after.
- `git checkout --theirs <file>` (or `--ours`) during a conflict takes the whole file, not the hunk.
- Generated branch names, `-v2` suffixes and dated names multiply in the branch list until nobody
  can tell which one is live. Rename at push time; reuse the PR's branch for later rounds.

## Verification

Before calling a git deliverable done:

- the branch is named to the standard (or the project's rule), based on the right integration
  branch, and contains only this concern;
- every commit message follows the repository's convention and explains why; where commits land as
  they are, there are no fixup, wip or "address review" commits left;
- the PR description is complete and current, its links are correct for its base, and its checks
  section lists only checks run on the current head;
- every review thread has a reply, and review was re-requested after the last round;
- no force push without a lease, no skipped hooks, no secrets or personal data in commits or PR
  text.

## Project specifics

The project's `AGENTS.md` or its own skill supplies: the integration and release branches, any
branch identity prefix, the commit-lint config, the PR template, who merges and by which method,
the merge-queue and release tooling, and how review identities are set up.

## Credits

Built on the git documentation (`git-push`, `git-rebase`, `git-range-diff`), GitHub's
documentation on pull requests, reviews, linking issues and branch rules, the Conventional Commits
and Conventional Branch specifications, Chris Beams' "How to Write a Git Commit Message", and
Google's engineering-practices guides on small changes and change descriptions (CC BY 3.0). Ideas
from the MIT-licensed `addyosmani/agent-skills` (git-workflow-and-versioning) and
`obra/superpowers` (finishing-a-development-branch) skills are re-expressed in our own words.
