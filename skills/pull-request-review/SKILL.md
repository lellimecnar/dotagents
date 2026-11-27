---
name: pull-request-review
description: Use when reviewing another agent's PR. Verify claims, pin head, one verdict.
metadata:
  version: "0.3.0"
  status: draft
  owner: maintainer
---

# Pull request review (verifying reviewer)

How a reviewing agent turns "the author says it works" into evidence, and posts a verdict that is
still true when someone reads it. The *judgement* half of review (what counts as a finding, how to
state it as a class, comment format, blocking vs non-blocking, verdicts, later rounds) lives in
`engineering-principles` → `references/review-protocol.md`. Apply that protocol; this skill adds the
verification mechanics, the head-pinning discipline and the reporting traps that make reviews get
bounced.

The GitHub mechanics (`gh pr view`, `gh pr review`, `gh api .../reviews`) are covered by the bundled
`github` skill; pre-commit self-review of your *own* change is `requesting-code-review`. Comment
kinds, suggested changes, thread replies and who resolves a thread are in `git-and-github-workflow`
section 8.

## When to use

- A task asks you to review a PR written by another agent or model, or a human's PR.
- A re-review round names an incremental range and prior findings.
- QA or the owner sends a review back for completeness.
- You run a periodic sweep that makes sure every open PR gets a review (see "Review sweeps").

Not for merging. Reviewers never merge; whoever owns the merge queue does.

## Principles

- **The author's report is a claim, not evidence.** Every "passes", "now served", "fixed
  everywhere" is something you re-establish yourself in this session.
- **Review exactly one head, and prove it.** Pin the full SHA at the start, review that tree, and
  re-check it in the same command that posts. A verdict attached to a head nobody looked at is
  worse than no verdict.
- **A partial implementation of a decision is the most common correctness gap.** Read the
  governing spec/decision in full for the sections the PR cites and list *every* rule in it, not
  only the one the author picked.
- **Judge against the requester's own words.** Written acceptance criteria summarize what was
  asked. When the task quotes the owner or requester, check the change against that quote too,
  and report any gap between the quote and the criteria as a finding.
- **Report completeness is part of correctness.** "N files fail X" without the file:line list is
  an incomplete finding, and a check listed as "pass" that you did not run in this session is a
  fabricated fact.
- **Review the change, not unrelated process.** Judge the pinned change in repository context against
  the governing request and rules. A change request names a concrete defect or unmet requirement and
  what must change, even if the problem is an omission with no changed line to annotate. Report
  unrelated handoff or delivery issues where the work is tracked, not as PR blockers. "Not enough
  evidence" with no named defect or unmet requirement is not a change request.
- **Proof you cannot produce is not a blocker by itself.** When a check needs something your
  environment lacks (a container runtime, a deployed environment, a live account), first look for
  equivalent evidence at the pinned head (provider CI, a check another authorized reviewer ran). If
  the diff then has no blocker, approve and state exactly what was not verified and how and when it
  can be. Hold the PR for a decision only when the unverified part could cause harm a revert cannot
  undo (data loss, security, money, production).

## Procedure

1. **Pin the head.** Read state, base, head SHA, author and changed files. Compare the *full* head
   SHA with the one the task names: task text sometimes carries a SHA whose short prefix matches but
   whose tail is wrong. Review the real head and note any mismatch in the verdict. If every agent
   pushes under one shared forge identity, the forge's "author" says nothing about which model wrote
   it; read commit authorship (`git log --format='%an <%ae>' <base>..<head>`) or the delivery card.
2. **Isolated checkout.** Fetch the PR head into its own worktree, never the shared checkout. If a
   local branch for that PR is already checked out in another worktree (a concurrent review), do
   not touch it: fetch and add a *detached* worktree at the pinned SHA instead. That also guarantees
   you review exactly the pinned tree. Record whether the branch contains the current base
   (`git merge-base --is-ancestor <base> <head>`); require it only where the project's rules do
   (a strict up-to-date rule, or a base change that affects this PR). Save the full diff to scratch and read all of it.
3. **Read the governing source.** Open the decision, spec, issue or owner request the PR claims to
   implement. Grep headings first and read the cited sections. Map each requirement to a line in
   the change.
4. **Verify claims against code.** For each "now implemented / now served / now enforced" claim,
   find the implementation (route, model, job, config) and cite file:line. For each "X lives in
   file Y" claim, grep Y for X; authors often cite the main file for something that lives in a
   sibling.
5. **Hunt siblings** (the scoped variant search in the review protocol). For each class of change,
   search the whole tree for instances the PR missed: the old wording, the old setting or job name,
   obligation words. Pages and files the PR did not open are the usual misses. Include docs,
   templates, instruction files and tool READMEs, not only the main source tree.
6. **Re-run every recorded check, in full-scan mode.** Read each checker's usage first: tools that
   default to "changed files" or "staged files" can exit 0 having inspected nothing. A checker that
   prints nothing and exits 0 has proved nothing until its output reports a count. When a full scan
   fails, run the same command on a clean base worktree to decide whether the PR caused it.
   Pre-existing failures are reported at their real severity, labelled *pre-existing*.
7. **Re-review rounds.** A request-changes review stays in force until the *same reviewer identity*
   posts a newer one, and it can start blocking later (for example once that identity's reviews
   begin to count). So post the fresh verdict at the new head as the identity that holds the old
   one, and confirm the PR's review decision actually flipped before handing off. Read
   `git diff <old>..<new>` in full, confirm each named prior finding
   against the new text (headings too, not only the quoted sentence), and re-run every prior
   finding's search across the whole tree. Still re-run every check on the full PR, not just the
   increment.
8. **Classify findings** per the review protocol: class sentence, principle or project-rule ID,
   blocking or not, and *every* instance with file:line and a concrete fix direction. This applies
   to pre-existing and non-blocking findings too.
9. **Self-audit before posting.** Every check in the pass list was actually run this session (do
   not copy a check from the task's checklist because it is "expected" to pass). Print every
   file:line you cite at the pinned head and read it: ranges supporting "this part is fine" go stale
   most often (wrong file, past EOF, a blank line).
10. **Post the verdict, guarded by a re-pin.** In one command: re-read the head, compare it to the
    pinned SHA, and only then post. A push during the review then aborts the post instead of
    attaching a stale verdict. Post as one review at the pinned commit (review protocol §9). The
    forge refuses approve/request-changes from the PR's own author identity; when reviewer and
    author share an identity, the verdict lands as a comment, and the profile should say which form
    to use. Build bodies from a file, never inline-quoted markdown.
11. **Hand off** to the tracker with the same body and the status the task names; keep the review
    or comment URL.
12. **Clean up** the worktree, any local PR branch, and any base-comparison worktree.

## Completeness bounces

When QA (or the owner) returns a review only to complete a finding while agreeing the verdict is
correct: do not change or re-post the verdict. Reproduce the missing instances yourself, because the
bounce's list is evidence, not fact. Re-pin, post a supplemental comment that names the original
review and says the verdict stands, then confirm the original review's state did not change.

## Provider CI is its own claim

"Checks pass" is two separate statements: the local gates you ran, and the live provider rollup at
the pinned head. Report both, separately.

- Query the provider's check runs for the head even when told CI is unavailable; never write "no
  workflow executed" without looking. If the review identity is refused access to the CI provider's
  run API or the forge's checks summary, read the per-commit check runs and their annotations
  instead, and report only the failure detail the annotations support: they are not full job logs.
- Page through check runs for each cited SHA and state what the query covers. For a historical
  attempt census, request all attempts where supported (GitHub example: the check-runs endpoint
  with `filter=all` and pagination). Report retrieval limits and the number actually examined.
- For each SHA, report the current provider rollup separately from historical attempts. Use the
  provider's active run or required-check identity for the current counts; never count a superseded
  attempt as active. Keep raw conclusions distinct (`neutral` is not `skipped`) and report pending
  states separately. Group failures by the actual failing step, distinguishing versions or pins
  when they differ.
- A failure also present on the base tip is *pre-existing / non-blocking*, not passing.
- **Runner-only failures: compare the same named job at the same SHA across attempts.** A checkout
  or workspace-cleanup permission error followed by success of that job at the identical SHA is a
  stronger runner control than green results from different jobs. Confirm the checkout step itself
  is unchanged and look for the same signature on unrelated work before calling it runner health.
- Report evidence from earlier SHAs separately from the active rollup. A cancelled predecessor run
  is not a current failure, and a pending aggregate is not a pass.

## Review sweeps

A sweep makes sure every open PR into the integration branch gets reviewed (and QA'd where the flow
requires it) without double work:

- Skip PRs another lane owns (bot dependency PRs usually belong to the dependency steward) and PRs
  that already have a review in flight or an agent review at the current head.
- Assign the reviewer from the *other* model family than the author: cross-model review catches
  what same-model review misses. Identify authorship from the project’s trusted records. If reviewer independence is required, verify it through authorized configuration or attestation.
  Never infer a model from an agent's name or a remembered roster; both drift. Record the eligibility result and date in the authorized review record, without unnecessary identifiers. Follow the project’s independence policy.
- Create review and QA tasks idempotently (a key that includes the date and PR number), with QA
  depending on the review so it starts only after.
- If a separate queue steward exists, use the configured handoff when required gates pass and avoid duplicate admissions.
- Cap rounds: after the third change request on the same PR, approve with the remaining
  non-critical findings listed as Known limits, or ask the lead or coordinator about a remaining
  critical disagreement; never start round four and never send it to the owner. A new commit, a new review task or a
  new sweep does not reset the count; check earlier change requests before opening a review for a
  revised head.
- A hold on merge admission is not a hold on review: keep reviewing PRs whose admission is held.
  If review itself is held pending a decision, record the review task with its release condition
  (in a blocked state, if your tracker has one), linked from where the change is tracked, so whoever owns the hold can
  release it without a duplicate dispatch.
- End with one line: nothing to review, or the list of queued reviews.

## Pitfalls

- Long checks in the foreground time out and lose output. Run them in the background, one log per
  checker with its real exit code, and poll a summary file. A pipe through `tail` hides the exit
  status.
- A small or `noexec` temp dir breaks tests that put stub binaries in `mktemp -d`, and native Node
  bindings (bundlers, test runners) fail to load from it. Check permissions, mount options and available space with the platform’s supported diagnostics; rerun in an authorized temporary location that meets the test’s requirements.
  More review-environment traps (temp dirs inside a repository, platform lock hashes, CPU quotas,
  native-addon ABI, offline policy-guard tests) are in
  [references/environment-traps.md](references/environment-traps.md): rule them out before
  attributing a red to the PR.
- If a frozen-lockfile install (`npm ci`, `uv sync --frozen`) fails, record the lockfile drift as a
  reproducibility finding. A non-frozen install in a throwaway worktree may continue the review; never
  commit the rewritten lockfile as part of the review.
- A committed `.patch` file always fails `git diff --check <base>...HEAD`. Authors who report
  "`git diff --check` passed" with no range on a clean tree checked nothing.
- If a second review task exists for the same head, say so; do not review twice.
- Performance scores: inspect the raw result (for Lighthouse, its JSON, including JSON extracted
  as data from an HTML report). Verify that base and head rendered the same representative fixture
  and reached the intended state; a successful response or early paint does not prove it. Match the
  repo's test config, browser and tool versions, CPU/network settings and cache state. If they
  differ materially, report the measurements as local characterization, not a verified delta.
  Preserve the assertions and reports in an approved location and redact sensitive data from shared
  copies. Check content visibility through a server-to-client swap separately from score or layout shift.
- When a server-rendered shell is replaced by an interactive view, verify accessibility in both
  states on the same representative fixture. Use test controls that delay the actual transition
  (for example, hold a client data request if it controls the swap). Assert that the shell is visible
  before the first scan; then release the transition, assert the loaded content and controls are
  present, and scan again. Apply the manual checks in `web-accessibility-check` where relevant.
  Use equivalent working-data states on the base before calling a failure a regression.
- For changes to protected instruction files, do not work around the write restriction. If the
  implementer cannot apply the edit, have them hand off an unapplied diff through an authorized
  channel. Only authorized roles may apply and publish it. Review the published head and verify
  that the application record identifies the authorized applier and matches the protected changes
  at that head. An earlier attestation or a signed or pushed commit alone is not proof of application.
- Review infrastructure write privileges and sensitive read access separately. Verify who can read
  current and retained state and plan artifacts without downloading or logging them by default.
  For example, Terraform's `sensitive` marker redacts display, not stored values. If inspection is
  necessary, have an authorized operator check in a restricted environment and report only redacted
  results. Mocks do not prove provider authentication. Check the real plan-time identity separately
  in an approved environment with least privilege, test state, and no apply or other unintended side effects.
- If the tracker has an execution policy, "done" may come back as "in review" with the next
  participant assigned. That is the handoff, not a failed write. Do not retry.

## References (by PR type)

- Docs PRs: `docs-drift-sweep` → `references/docs-verification-traps.md`.
- CI-gate, required-check and merge-queue config PRs: `merge-queue-stewardship` →
  `references/queue-config-review.md`.
- Release-train / batch PRs: `merge-queue-stewardship` → "Reviewing a train PR".
- Dependency / lockfile PRs: `dependency-update-review`.
- UI changes: `web-accessibility-check`. Behaviour acceptance: `qa-acceptance-evidence`.

## Verification

Before you call the review done: head re-pinned in the posting command; every pass-listed check has
a log from this session; every finding lists all instances with file:line; every cited range was
printed at the pinned head; pre-existing failures are labelled; worktrees removed.

## Project specifics

The project's own review skill or `AGENTS.md` supplies the base branch, worktree root, checker
commands and their full-scan flags, the forge identity rules, the tracker hand-off API and statuses,
and known flaky checks.
