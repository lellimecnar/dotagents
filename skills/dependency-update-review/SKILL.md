---
name: dependency-update-review
description: Use when triaging bot dependency PRs or lockfile changes. Reconcile, verdict.
metadata:
  version: "0.1.2"
  status: draft
  owner: maintainer
---

# Dependency update review

How a dependency steward turns a queue of bot-opened update PRs (Dependabot, Renovate) or a
consolidated lockfile PR into recorded, head-bound verdicts that a merge queue can act on. It covers
the single-PR method, queue audits, recording verdicts durably, and editing the bot's config.

## When to use

- A daily/weekly sweep of open bot dependency PRs.
- Reviewing a PR that changes only manifests and lockfiles, including one that consolidates or
  supersedes several bot PRs.
- Editing the update bot's configuration (grouping, ignores, schedules).

## Principles

- **Fix the lockfile before judging the bump.** The most common defect in bot PRs is a manifest
  bump without the matching lock update. Until the lock resolves, "did it resolve cleanly" is a
  vacuous question and every downstream conclusion is guesswork.
- **A resolver failure is the finding.** Capture the resolver output verbatim; it is usually the
  strongest evidence in the review and normally means Hold or Close.
- **Audit resolved versions, not the description.** After a rebase or relock, the PR description
  usually describes an older head. Raising a floor makes resolvers pick the newest release, so a
  patch-floor bump can land a minor or 0.x jump.
- **Review and admission are separate.** You record verdicts; you never merge. A final merge
  verdict is a signal for whoever owns the merge queue (see `merge-queue-stewardship`).
- **A verdict only in the transcript is not a verdict.** Every PR gets a label (the index) and a
  comment (the record), both naming the head they apply to.
- **Act only under the bot's identity.** Commit, comment and open PRs as the bot account the
  profile names, never under the owner's identity or credentials: work done in the owner's name
  cannot be told apart from the owner's own. If only the owner's credential is available, stop and
  ask.
- **A verdict in the tracker is not a verdict on the forge.** If the base branch requires an approving
  review, an internal merge recommendation alone does not meet the gate: the forge must recognize an eligible approving review on the exact head. A Close verdict ends with the PR actually closed. Verify completion on the forge, not only in the local tracker.
- **Hold means "not yet", Close means "not this".** Never downgrade a Hold to Close because the
  unblock looks like work.

## Verdict vocabulary

| Verdict | Meaning | Resolution |
| --- | --- | --- |
| Merge | Safe, low risk | Admitted to the merge queue |
| Verify | Safe pending specific *named* checks | Run those checks, then re-adjudicate to Merge/Hold/Close |
| Investigate | Evidence insufficient for a verdict | Research the named question, then re-adjudicate |
| Hold | Breaking change; needs code work first | Unblock PR, then re-verdict the bump at its new head |
| Close | Invalid, unwanted, superseded, or targets the wrong base | Comment the reason, then close |

Verify and Investigate are *interim*: a run is not finished while either remains. Close examples: a
non-LTS base image, a bump that breaks a lockstep pin, a bump superseded by a newer PR, a package
no longer used, a PR against the wrong base branch. If a Close reason will recur every week, fix it
at the source (an `ignore` entry, or a recorded pin rationale) in its own PR.

## Single-PR method

1. **Fetch.** Packages, old → new versions, bump type, base branch. A bot PR against the wrong base
   is a config bug: report it.
2. **Reconcile the lockfile** in an isolated worktree from the pinned head (never switch the shared
   checkout's branch). Regenerate with the ecosystem's lock command. Commit only the lockfile; if
   regeneration also changes a manifest, the bump is not self-consistent — record that and leave it.
   If the lock is already current, change nothing. Pushing to a bot's branch ends the bot's
   management of that PR: it stops rebasing it, and the PR is now yours to carry to merge or close.
   Treat that handoff as conditional. Verify whether a manual commit, including lockfile
   regeneration, stops the configured bot from rebasing that PR. If it does, record who owns rebases in the PR or task tracker before requesting another rebase.
3. **Changelog and breaking changes** between old and new, covering every intermediate version on
   minor/major bumps. Look in the repo changelog, forge releases, the registry page, and — before
   declaring "no changelog" — the tag comparison and the release PR bodies (release-automation tools
   can keep the only complete notes there). Order findings: breaking changes (with whether this repo
   is affected and the concrete fix), deprecations (a deprecation can turn a lint job red, making it
   a blocker), security fixes, then relevant notable changes.
4. **Codebase impact.** Which manifest and workspace; dev/test-only vs production; grep imports and
   symbols across source and tests; group usage by feature area in plain language. For
   static-analysis tool upgrades, turn parser/default changes into a source scan for the exact syntax
   the changelog names, rather than trusting a clean tool run.
5. **Run the gates the change can break**: the affected service's lint, typecheck and tests (a new
   linter minor can add rules, so lint *is* the real check of a linter bump); a runtime smoke for
   server-framework bumps (start the app in the background, probe health in a separate call); the
   lock check with the *CI-pinned* tool version as well as your local one.
6. **Other declarations.** Grep every manifest for the package so a sibling service with a
   conflicting floor or the same pending bump is not missed.
7. **Verdict** with a one-to-three-sentence reason, weighing bump type, production vs tooling,
   feature area (auth, billing, gateways rank above tooling), changelog breakage and security. Say
   what a human should verify that CI cannot: runtime behaviour, deployed-version skew, new
   deprecation warnings in logs. Do not recommend "run the test suite"; CI does that.
8. **Record it** (label + comment, see [references/verdict-records.md](references/verdict-records.md)).
9. **Resolve it on the forge.**
   - Merge: confirm the PR head still equals the head named in the verdict, then make sure an
     APPROVE review exists for that exact commit (GitHub: `POST /repos/{o}/{r}/pulls/{n}/reviews`
     with `event=APPROVE` and `commit_id=<head>`). Verify the review decision reads approved
     (`gh pr view <n> --json reviewDecision`).
   - Close: post the verdict comment, close the PR (add the bot's ignore command only if the verdict
     says so), verify the state is closed.

## Queue audit

- Discover PRs yourself; never ask the user to paste URLs. Announce the scope in one line.
- Reconcile then gather evidence per PR, one isolated worktree each. Before pushing lock commits to
  several sibling PRs in one service, model their merge order from pinned heads (a synthetic
  `git merge-tree` chain avoids checkout churn) and record every conflict: siblings edit the same
  lock blocks, and after a lock push nobody rebases them automatically.
- Adjudicate, then **resolve every Verify** (run exactly the named checks) and **every Investigate**
  (research: upstream release notes and migration guides, this repo's call sites, the advisory and
  whether the vulnerable path is reachable, sibling packages that pin it). The research must reach
  the PR comment.
- Report: summary table (`# | Package | Bump | Type | Eco | Age | Verdict | Why`), sorted
  Merge → Verify → Investigate → Hold → Close, oldest first within a bucket, security advisories on
  top. Then per-PR detail and a worklist.
- Closes run after merges land, so a Close citing a merged PR stays accurate. Holds are routed to
  unblock work or handed back as a named list.
- Finish with a phase report: admitted, observed merged, closed, held (one clause each), blocked,
  "no interim labels remain", deploy result, worktrees removed.

## Consolidated / lockfile-only PRs

- **Floors match what they supersede.** For each superseded bot PR, diff its specifier changes
  against the consolidated manifest and record whether it is already closed. Consolidations silently
  drop bumps.
- **Lock consistent with manifest** under the CI-pinned tool version. A changed lock-format header
  means a newer tool wrote it; prove compatibility with the pinned binary rather than inferring it.
- **Flag each minor/major jump and each stale description claim** (versions, rollback plan after a
  merge commit) as its own finding class.
- **Formatter drift** that also exists on the base is pre-existing and ungated: report it with the
  full file list for an owner decision; it does not block the dependency PR.

## Editing the update bot's config

- Read the schema from the bot's raw documentation source; rendered pages can truncate.
- Grouping facts (Dependabot): `directories:` merges config only; GitHub still opens each group's PR
  *per directory*. `group-by: dependency-name` is the only cross-directory option and yields one PR
  per dependency. A dependency joins the *first* group it matches, so use disjoint `update-types`
  (minor+patch vs major) to make order irrelevant. Never promise "one PR per ecosystem"; a release
  train is what gives the owner one merge.
- Report manifests with no config entry as coverage gaps; do not silently widen scope.
- Regrouping can leave open PRs tied to removed rules. For example, Dependabot may report that
  the `dependabot.yml` entry that created a PR was removed. Check the configured bot’s supported recovery
  options and record how each affected PR will be handled.
- State in docs and PR text only behaviour you verified in the bot's docs or changelog; reviewers
  diff claims against the docs. When editing a skill or doc that describes the flow, grep it for
  every leftover merge instruction before resubmitting.

## Pitfalls

Ecosystem-specific traps (Go toolchain floors, Docker ignore matching, runtime majors, CI action
majors, OpenTelemetry release streams, Python lock tooling) are in
[references/ecosystem-traps.md](references/ecosystem-traps.md).

- Before blaming a red check on the bump, confirm it is not red on the base tip.
- **Diagnose red CI before touching the branch.** Does the required check exist in the workflow at
  that head? Inspect the available CI evidence, or ask an authorized operator to confirm the failure mode. Runner or workspace failures (permission errors during
  checkout, a dirty persistent workspace) are CI health: report the problem to the responsible operator with
  evidence they may access. Retry isolated runner failures only when authorized; do not rebase
  unrelated branches to trigger new runs.
- **Use the ref that governs the claim.** Verify base facts against the current base revision
  and proposed changes against the pinned head. Do not rely on unverified working-tree state.
- **A queued verdict's head goes stale when the bot rebases.** Re-read the head before answering;
  report the drift and whether the files or call sites you audited changed.
- **The last pusher cannot approve.** With "require approval of the most recent push" (GitHub
  ruleset `require_last_push_approval`), pushing a lockfile commit makes your own APPROVE count for
  nothing. Obtain approval on that head from someone eligible under the project’s branch policy.
  Do not self-approve and call the PR done; check the review decision.
- Every worktree you create is removed on every exit path; a leaked one is indistinguishable from
  real work. Check for uncommitted unique work before forcing removal.
- Infrastructure-provider bumps (Terraform etc.) affect live infra: never apply; a plan against live
  infra needs the owner to name the action.

## Verification

Exactly one verdict label per PR, matching the comment, both naming the current full head; no
interim verdicts left; every Hold has an unblock path; every Close has a posted reason; local
checks and provider rollup reported as separate claims.

At the end of the run, verify each current-head merge recommendation against any required approval gate.
Verify that each final close decision shows a closed PR on the forge. Resolve or report exceptions as authorized.

## Project specifics

The project's dependency skill or `AGENTS.md` supplies the base branch, ecosystems and lock
commands per directory, label names and colours, the comment marker, known-flaky checks, lockstep
pins, who adjudicates (some projects require a specific model to issue verdicts), which identity
submits the approving review, the merge queue,
and the weekly schedule.
