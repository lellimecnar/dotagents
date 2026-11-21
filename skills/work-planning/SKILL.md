---
name: work-planning
description: Use when a plan or spec is requested, or uncertainty must be resolved before work.
metadata:
  version: "0.1.1"
  status: draft
  owner: maintainer
---

# Work planning

A plan exists to make the work go right the first time: everyone agrees what "done" looks like, the risky
parts are known before anyone builds, and the person who approves can see what they are approving. Plan only
as much as the work needs. A small, clear, already-authorized next step is better done than planned.

## When to use
- Someone asks for a plan, spec, design or proposal.
- The work is big enough, risky enough or unclear enough that starting without agreement would waste effort.
- Several people or agents will work on it, or a human must approve before it starts.

Skip it when the next step is small, obvious and authorized: do it and report.

## What a plan contains
1. **Outcome:** what will be true when this is done, in the requester's terms. Quote their words when you have them.
2. **Scope and non-goals:** what is in, and what is deliberately out, so nobody fills the gap with guesses.
3. **Constraints:** rules, deadlines, budgets, compatibility, owner decisions that bind the approach.
4. **Approach:** the chosen way, and the main alternative you rejected with one line on why.
5. **Risks and unknowns:** what could make this fail, and how each will be retired (a spike, a question, a test).
6. **Verification:** how anyone will know it worked: the observable checks, not "it should work".
7. **Open questions:** only the decisions that block or change the work, each with your recommended answer.

Write it in the requester's preferred format and at the depth the work deserves; a one-paragraph plan is often right.

## Splitting into tasks
- Keep a cohesive piece of work with one owner. Split only when it earns it: a different owner or skill, useful
  parallel work, a real dependency, or an independent review.
- Each task names its outcome, acceptance criteria, dependencies and how it will be verified, so whoever picks it
  up needs nothing from your head. Link dependencies in the tracker rather than describing them in prose.
- Anything owed after the plan (follow-ups, prerequisites, owner steps) becomes its own task with an owner. A plan
  that relies on "someone will remember" is incomplete.

## Coordinating tasks in flight
When several workers share a tracker and tasks are already running:
- Refer to every task by its tracker id in bodies and comments, never "the review task". Workers that cannot
  list the board find existing work only through ids; an unnamed task gets recreated, and two workers then
  edit the same files.
- When you authorize a change that contradicts the acceptance criteria of a downstream task you already
  created, amend that task's criteria at once and name the exact allowed exception. Otherwise its checker
  correctly fails on the stale contract and stalls the chain.
- A correction says exactly what it supersedes ("replaces item 1 of the body", "supersedes the comment of
  <time>"). Otherwise the worker follows both.
- When a task waits on another, link the other as its dependency in the tracker before you mark it blocked.
  A blocked task with no linked blocker reads as a question for a person and pings them for nothing.

## Approval
- Respect explicit planning mode and approval gates: present the latest revision and wait for the decision.
- Keep one current revision. Update it in place instead of creating copies, and say what changed.
- After approval, execute; don't re-plan unless new facts invalidate the plan, and then say which fact.

## Pitfalls
- Planning to avoid doing: if the plan is longer than the work, do the work.
- Hidden decisions: an assumption that changes the outcome belongs in open questions, not in the approach.
- Vague verification ("tested", "looks good"): name the check and its expected result.
- Over-splitting: ten tiny dependent tasks cost more coordination than they save.

## Project specifics
Where plans are saved (repo docs, tracker cards, attachments), the card and naming conventions, and who approves
come from the project's own skills and `AGENTS.md`.
