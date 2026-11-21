---
name: backlog-grooming
description: Use when grooming a backlog: one state per item, verify claims, owner unblocks.
metadata:
  version: "0.1.1"
  status: draft
  owner: maintainer
---

# Backlog grooming

A recurring pass (usually daily, before the standup) whose goal is to start each day with a clean
backlog: well-defined work that is ready, a clear picture of what is blocked and why, and a short
list of things only the owner can unblock.

## When to use

- A scheduled grooming run over a task board or issue tracker.
- A single "backlog" item filed by another role (SRE, tech-debt review) that needs a state.

## Principles

- **Every open item gets exactly one state**, with a one-line reason and an evidence link. A
  workable vocabulary: ready / needs-stories / needs-definition / needs-research / needs-spike /
  needs-design / blocked / needs-owner / done / superseded / overruled / redundant /
  consolidate-into.
- **Verify, don't trust, the item's own claims.** Check against git, the forge and the repo docs on
  the integration branch. "Closed" is not "landed": prove landing with an ancestor check
  (`git merge-base --is-ancestor <head> origin/<branch>`), because release routines can close source
  PRs unmerged.
- **Reproduce a claimed blocker before it reaches the owner.** When an agent reports "no access to X",
  verify it in the affected execution context using an approved read-only check and equivalent authorized permissions, or request evidence from its operator. If
  it works, send the item back to the agent with the evidence instead of listing it for the owner. If
  you cannot reach that runtime, say your test was not equivalent. Recheck unresolved blockers against current evidence before escalating or relaying them.
- **Never close, cancel or merge anything.** List it for the owner with evidence. Grooming changes
  priority, edges and definition; it does not end work.
- **The owner's attention is the scarce resource.** Put the owner unblock list first, short (about
  ten), each item answerable in one sentence, with a link.
- **Count mechanically.** Hand counts drift on the first run.

## Procedure

1. **Gather.** Every open item in every open status, plus items completed since the last grooming.
   Fetch details (blockers, blocked-by, review state) and the last couple of comments. Some trackers
   omit conversation/chat items from list endpoints; probe numbering gaps to find them. Read the
   owner's recent messages and operations replies before classifying, and refresh the list right
   before writing (others act while you work).
2. **Baseline.** Read the previous grooming result; you will report what changed.
3. **Classify** every item with one state, reason and evidence.
4. **Act where allowed**, highest value first:
   - add missing blocker edges; unblock items whose blockers are done;
   - repoint edges from cancelled or superseded blockers to their live replacements (cancelled
     blockers never resolve, so the dependent item never wakes);
   - state a priority for each item;
   - write stories for needs-stories items (owner quote, acceptance criteria, suggested implementer
     and cross-model reviewer) and mark them ready.
   If the tracker caps writes per run, rank actions by value (edges and unblocks before
   priority-only changes on superseded items), and list skipped actions for the next run.
5. **Publish** the day's result in one place, first line `Backlog <date>`:
   owner unblock list → ready to work by priority with suggested owner → needs
   definition/research/spike/design with the question each must answer → for owner sign-off (done,
   superseded, overruled, redundant, consolidate) with evidence → what changed since yesterday.
6. End with one summary line (ready count, owner unblocks, sign-off count) and the owner list.

## Stuck patterns worth checking every run

- **Review rounds exhausted**: an item in review whose current participant is the owner and whose
  change-request count equals the cap. The owner must decide (approve or another round).
- **Approval of review tasks**: approving a review task accepts the review work, not the PR. Group
  them into one owner line.
- **Blocked with no blocker** and an exhausted repair loop: an owner item. Do not re-toggle the
  status; it loops.
- **Blocked with no edge, no pending interaction, no recovery action**, where the owner step is
  verifiably done: move it back to ready and post the evidence.
- **Agents in an error state** leave their routine items blocked with no edge. The reset is an owner
  item.
- **A reviewer that never commented for days** on an item in review was probably never woken: a
  standup item, not a frozen item.
- **Duplicate routine items**: routines that "skip if active" may still spawn a new item while the
  previous run is blocked. The newest supersedes the rest.
- **Superseded review tasks**: reviews pinned to an old PR head are superseded by the review of the
  current head. Bot dependency PRs closed as "superseded by #N" make their verdict tasks redundant;
  diff verdict-task PR numbers against open PRs.
- **Orders never applied**: check-in or escalation verdicts written as comments are often not
  applied by the assignee. Read them and apply the edge/status changes they order, citing them.
- **Owner-authored PRs with changes requested and no fix task**: ask whether agents should fix them
  before writing stories.
- **Integration-branch CI** checked by hand: list recent runs and read the failing job's annotations
  (failed-log output can come back empty).

## Verification

Every open item has a state, reason and evidence; every count came from a query; nothing was closed
or cancelled; skipped actions are listed; the owner list is at the top and short.

## Project specifics

The project's grooming skill or `AGENTS.md` supplies the schedule, the board/tracker calls, where
the result is posted, write-budget limits, the item title conventions, tracker-specific frozen-item
signals, archive locations for retired trackers, and the standup that consumes the result.
