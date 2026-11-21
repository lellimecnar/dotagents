---
name: tech-debt-ranking
description: Use when producing a recurring ranked, deduped tech-debt backlog. No code edits.
metadata:
  version: "0.1.1"
  status: draft
  owner: maintainer
---

# Tech-debt ranking

A recurring read-only pass (once or twice a week suits most teams) that finds the debt costing the
project most, dedupes it against what is already owned, files a small number of well-evidenced
backlog items, and publishes a ranked list with what changed since last time. It makes **no code
changes**; fixing debt is someone else's task.

## When to use

- A scheduled tech-debt routine, or a request for "a ranked tech-debt backlog of this repo".
- Not for fixing debt, and not for incident response (`alert-triage`).

## Principles

- **A dead pipeline outranks everything.** If CI or deploy is broken, nothing else ships; rank it
  critical even if someone mentioned it in prose. A mention in another issue's comments is not an
  owned issue.
- **Few, well-evidenced items beat a long list.** Cap new items per run (about ten). Each has
  evidence, the cost of leaving it, a suggested priority, and a "done when".
- **Dedupe before filing.** Against open tracker items, earlier runs' items, initiative/inventory
  docs (a component slated for replacement is not debt to file), and forge issues (older ones may be
  titled but unlabelled). Link related items instead of duplicating them.
- **Every count you cite comes from a query you ran.** A guessed count in the ranking is a
  fabricated fact.
- **Coverage is part of the deliverable.** Inputs you could not read are listed as coverage gaps,
  with the reason; they are not silently skipped.

## Procedure

1. **Prior state.** Read the previous run's ranking (the "what changed" section diffs against it)
   and the open items earlier runs filed; update or skip those instead of re-filing.
2. **Snapshot without touching a shared checkout.** Fetch the integration branch and read through
   it (`git grep … origin/<branch>`, `git show origin/<branch>:<path>`, `git ls-tree -r`); never
   check out or pull in a checkout another run may hold.
3. **CI health first.**
   - Find the latest run that actually started. Use the provider’s documented run and job states to distinguish a run that never started from a started job that failed. Verify the cause before attributing it. Leave
     such runs out of failure and flakiness rates; if runs keep failing to start, file that as its own
     finding with the evidence.
   - Per-workflow failure rate and median duration over a recent window.
   - For a chronically red workflow, see which step fails. All jobs failing at the same setup step
     is an infrastructure defect: file it.
   - Runs on the integration branch triggered by a *pull request* event come from a promotion PR,
     only if its actual event and refs match the configured promotion workflow. Check triggers and base/head refs; count PR-event runs separately from integration pushes.
   - A workflow red on every run since its PR merged, whose task is still in review, is a live
     defect: file it *and* post one evidence comment on that task, because a backlog item will not
     reach anyone before the review finishes.
4. **Recurring alerts.** Count alert fingerprints that keep re-firing, and triage runs that ended as
   dedup-only per fingerprint: repeated wakes with no new signal are token-waste debt. Many alert
   rules sitting in "no data" while telemetry is dark means the alerting itself has a gap — a debt
   item even when the outage has an owner.
5. **Held-back majors.** Major bumps (toolchains, CI action majors, SDK 0.x→1.x) open for weeks, and
   human PRs that conflict with a bot PR on the same package. A runtime older than its supported
   window (e.g. Go supports only the two newest releases) is high.
6. **Markers.** `git grep -I -nE '\b(TODO|FIXME|HACK|XXX)\b'` excluding docs, lockfiles and JSON
   (`-I` avoids bogus "binary file" hits). Prioritise TODOs with an expired "safe to remove after
   <date>", TODOs describing a correctness or data-integrity gap (unguarded post-commit side
   effects), and stubs that always allow. Grep a stub's callers: no callers means dead code (low),
   not a live hole. Placeholders owned by a milestone are not debt.
7. **Oversized modules.** Line-count source files (excluding fixtures, tests, generated code) and
   compare with the project's size caps.
8. **File items.** A fixed title prefix (so next run's dedupe is a single search), backlog status,
   suggested priority, and *no parent tied to the run* (the run closes; the backlog must outlive
   it). Body: Evidence (paths, counts, run ids) / Cost of leaving it / Suggested priority (with an
   owner-gated note when the fix is a human decision) / Done when. Cross-link related items after
   creation. Build bodies from files so newlines survive.
9. **Ranking document.** Ranked table (item, priority, debt, cost) → What changed since last run →
   Inputs and coverage (every input, including unreadable ones and why, and what was deliberately
   not re-filed and which item owns it) → Next run. Read it back before closing the run.
10. If nothing is new and the ranking is unchanged, one line and stop. Otherwise end with one line:
    new item count and top item.

## Pitfalls

- Credentials for observability tools may not be forwarded into a sandbox. If they are missing,
  fall back to alert tracker items and triage run comments, and log the gap in coverage; do not file
  a new item if that access blocker is already tracked.
- When scripting the tracker API in an unattended session where code execution is blocked, put a
  small curl helper in a sourced file and drive it from the shell with `jq`.

## Verification

Every item has evidence a reader can re-run; every count has its query; the coverage section names
every input; no item duplicates an open one; the item cap was respected.

## Project specifics

The project's tech-debt skill or `AGENTS.md` supplies the schedule, the tracker and its title
conventions, size caps and lint config, which docs mark components for replacement, where alert
fingerprints live, and who grooms the filed items.
