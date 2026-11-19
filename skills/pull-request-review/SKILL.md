---
name: pull-request-review
description: Use when reviewing another agent's PR. Verify claims, pin head, one verdict.
metadata:
  version: "0.1.0"
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
`github` skill; pre-commit self-review of your *own* change is `requesting-code-review`.

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

## Procedure

1. **Pin the head.** Read state, base, head SHA, author and changed files. Compare the *full* head
   SHA with the one the task names: task text sometimes carries a SHA whose short prefix matches but
   whose tail is wrong. Review the real head and note any mismatch in the verdict. If every agent
   pushes under one shared forge identity, the forge's "author" says nothing about which model wrote
   it; read commit authorship (`git log --format='%an <%ae>' <base>..<head>`) or the delivery card.
2. **Isolated checkout.** Fetch the PR head into its own worktree, never the shared checkout. If a
   local branch for that PR is already checked out in another worktree (a concurrent review), do
   not touch it: fetch and add a *detached* worktree at the pinned SHA instead. That also guarantees
   you review exactly the pinned tree. Confirm the branch contains the current base
   (`git merge-base --is-ancestor <base> <head>`). Save the full diff to scratch and read all of it.
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
7. **Re-review rounds.** Read `git diff <old>..<new>` in full, confirm each named prior finding
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
  workflow executed" without looking.
- Give exact counts by conclusion for every head you cite, and group failures by failing *step*
  (checkout variants differ by version or pin; do not write "N failures, same step" without
  grouping).
- A commit can carry several historical runs; group by run/job identity before totalling.
- A failure also present on the base tip is *pre-existing / non-blocking*, not passing.

## Review sweeps

A sweep makes sure every open PR into the integration branch gets reviewed (and QA'd where the flow
requires it) without double work:

- Skip PRs another lane owns (bot dependency PRs usually belong to the dependency steward) and PRs
  that already have a review in flight or an agent review at the current head.
- Assign the reviewer from the *other* model family than the author: cross-model review catches
  what same-model review misses. Never review your own family's work if the profile forbids it.
- Create review and QA tasks idempotently (a key that includes the date and PR number), with QA
  depending on the review so it starts only after.
- Cap rounds: after the third change request on the same PR, escalate the disagreement to the owner
  as one decidable sentence rather than starting round four.
- End with one line: nothing to review, or the list of queued reviews.

## Pitfalls

- Long checks in the foreground time out and lose output. Run them in the background, one log per
  checker with its real exit code, and poll a summary file. A pipe through `tail` hides the exit
  status.
- A small or `noexec` temp dir breaks tests that put stub binaries in `mktemp -d`. Re-run with a
  temp dir on a real disk before blaming the PR.
- A committed `.patch` file always fails `git diff --check <base>...HEAD`. Authors who report
  "`git diff --check` passed" with no range on a clean tree checked nothing.
- If a second review task exists for the same head, say so; do not review twice.
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
