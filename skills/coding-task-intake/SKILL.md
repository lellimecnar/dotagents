---
name: coding-task-intake
description: Use when taking a coding task end to end: intake, build, verify, PR, report.
metadata:
  version: "0.1.0"
  status: draft
  owner: maintainer
---

# Coding task intake to report

The working loop for an agent that receives a coding task (from a card, a ticket, or chat) and must
hand back a reviewable PR with evidence. Coding principles themselves are in
`engineering-principles`; tests-first is `test-driven-development`; failures are
`systematic-debugging`; your own pre-push review is `requesting-code-review`; GitHub mechanics are
`github`.

## When to use

Every coding task: features, fixes, redesigns, small content changes in a code repo.

## Principles

- **The requester's own words outrank any summary of them.** Read the whole task, comments, parent
  results and attachments before deciding what "done" means.
- **Decide rather than stall, and make the decision visible.** If a reasonable engineer could pick a
  sensible default, pick it and list it under "Decisions to confirm" with your default. Block with
  ONE lettered question only when the task is too vague to start *and* no sensible default exists,
  or it needs something only the owner has (content, accounts, money).
- **Smallest complete change.** State every layer the request reaches (caller, shared helper,
  persistence, API/schema, component, fixture, test, config, docs). Search for an existing helper
  and its callers before adding code; prefer deletion, reuse, standard library, then an installed
  dependency, in that order. Never smuggle a new dependency into a change.
- **Done means verified.** A killed or dependency-blocked run is not a passing run. Looking at the
  running result is part of verification for anything a user sees.
- **Never bypass hooks.** A failing hook is a real failure: fix the cause, or block naming the tool
  the environment lacks.

## Procedure

1. **Intake (first minutes).** Goal, repo, what done looks like, what needs the owner. Check overlap:
   your open PRs and branches for the same goal; extend an open PR instead of starting a parallel
   one.
2. **Workspace.** Clone once and fetch with prune. One worktree per task from the up-to-date default
   (or integration) branch, on a branch named for your role and the task; to continue a PR, add a
   worktree on its branch. Install with the lockfile (frozen). Run long installs, builds and suites
   in the background with a log, and read the log rather than waiting on a foreground timeout.
3. **Build.** Read the repo's `AGENTS.md`/`CLAUDE.md` (root and nearest package) and any repo skill
   first. Small, reviewable commits with conventional messages. Tests for logic you add or change;
   one regression test before a non-trivial fix, shown to fail for the right reason. For UI work,
   check accessibility (`web-accessibility-check`) and performance (image sizes, fonts, client JS).
4. **Large work** (a redesign, a new app): phases that each end in a working, pushed state, so
   progress survives a restart. Heartbeat progress on the task at a regular interval. Delegate
   self-contained pieces, then review and integrate them yourself.
5. **Verify before every push, all of it:**
   - The repo's own checks for the packages you touched: lint, typecheck, test, build. Record each
     command, exit code, count, duration and any deselection.
   - Run the app and look at every changed page in a real browser, at a desktop and a phone width,
     and in light and dark themes if supported. Save the important screenshots for the PR. If the
     browser cannot reach local addresses, push and check the preview deployment instead.
   - Secret scan of the staged diff; it must print nothing. A starting pattern:
     `git diff --cached | grep -nE 'gh[pousr]_[A-Za-z0-9]{20,}|sk-[A-Za-z0-9]{20,}|AKIA[0-9A-Z]{16}|BEGIN [A-Z ]*PRIVATE KEY|xox[baprs]-'`.
     Also scan for personal data.
6. **PR.** Push the branch and open the PR against the right base; draft when it is not ready for
   review (phased work, open decisions). A draft is a progress update, not a stopping point unless a draft
   was requested or a verified blocker prevents continuation. Body sections: **What and why** (2–4 lines) /
   **Screenshots** / **Checks** (each command and result) / **Decisions to confirm** (lettered, each
   with your default) / **Blockers** (genuine blockers only, with evidence: owner-only actions, outside
   dependencies or hard limits). Finish the work the task owes before calling the PR ready, per
   `finish-the-whole-request`. If the platform builds preview deployments,
   wait for success and link it; a failed preview build is a failed task — read its log or reproduce
   the build locally. Never merge, never push to the default branch, never close someone else's PR.
7. **Report.** Line 1 is the complete short answer, because many wake messages show only line 1:
   `PR #<n> ready for review: <title> - preview <url>` / `Requested draft PR #<n>: <title>` /
   `No change needed: <reason>` / `Incomplete: <verified blocker> - <actual trigger or owner-only action, if any>`. Then `Changed:` (one
   line per area), `Verified:` (checks and pages viewed), `Decisions:` (lettered, or none), `Blockers:` (or none).
   Attach structured metadata (repo, PR number and URL, branch, preview URL) where the tracker
   supports it.
8. **Learn.** Save durable engineering facts (commands that work, pitfalls, the owner's design
   decisions) to memory, and fix the repo skill when a command in it was wrong.

When the requester is in chat rather than on a card, run the same loop and send one-line updates
per phase, with PR and preview links as soon as they exist.

## Pitfalls

- A lockfile mismatch on clean install is a reproducibility failure to report, not permission to
  rewrite the lockfile during review.
- Never infer health from an empty base dependency list; install the extras the selected tests need.
- `/tmp` mounted `noexec` breaks native bindings and stub binaries; work from a real disk path.
- Before re-filing a blocked task as a new one, check the related tasks' checkouts for committed work: a worker
  that blocked for lack of a workspace may already have the same fix committed in another task's checkout.
  Re-filing does the work twice.
- Trust a checkout outside the tracker's managed worktrees only after `git fsck --connectivity-only`: such trees can
  lose objects, leaving commits that exist but cannot be pushed. Salvage the content as a patch (`diff -u` against
  the target branch, then `git apply --check` in a healthy worktree) instead of rebuilding the fix by hand.

## Verification

Checks recorded with exit codes; pages viewed at two widths (and both themes); secret scan clean;
PR body complete; report line 1 stands alone.

## Project specifics

The project's intake skill or `AGENTS.md` supplies the repo list and clone paths, branch naming,
install and check commands, the preview-deployment lookup, the tracker calls (show, block,
heartbeat, complete), and who reviews.
