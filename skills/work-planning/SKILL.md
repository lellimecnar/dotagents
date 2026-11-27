---
name: work-planning
description: Use when a plan or spec is requested, or uncertainty must be resolved before work.
metadata:
  version: "0.1.2"
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

## Building on earlier work
Before planning, find any merged or in-flight plan, study or decision that covers the same ground, and judge the
new input against it. The result is usually an amendment to that plan or a small spike that reuses its setup, not
a fresh plan. Treat recorded decisions as constraints. Reopen one only when asked or when new evidence
invalidates it; name the evidence and seek approval before changing the decision.

## Experiment and spike plans
The requester should be able to hand an experiment, spike or proof-of-concept plan to a capable newcomer who
follows it without error. A long research spec of hypotheses does not meet that bar; a runbook does:
- For each step, name its goal, prerequisites, exact commands or manual actions, inputs, expected outputs,
  how to check them, what to do if the check fails, and the estimated time. Pin file and code inputs (for
  example, by checksum or commit SHA) and state expected data size. Give paths for saved outputs, schemas
  and an example row for tabular outputs, and the expected result for each check.
- Fix confirmatory rules and decision thresholds before inspecting the evidence used to decide the result.
  Keep exploratory runs separate and give worked examples for ambiguous rules. Report accuracy against
  validated ground truth. Report matches to unvalidated labels as agreement, not accuracy. State unknown
  provenance and do not claim independence unless it is established.
- Prove it can be followed: the author runs the setup and data-acquisition steps verbatim from an empty directory
  before asking for review and fixes every step that failed or needed a guess; the reviewer repeats that walk in
  its own checkout. For an input that cannot be byte-pinned (a page that changes on every fetch), archive the
  fetched snapshot with its checksum (or the parsed output, if that is enough to replay), and the plan says so.
- Before a proposed external dataset or tool goes into the plan, check it live: licence, last activity, and
  whether it installs and runs where the work will run.
- State a scope cap: the smallest measurement that answers the question and an effort target. Reuse data
  already held if it can answer the question; otherwise say what minimum new data is needed and why.

## Review rounds add rigour, not scope
Each review round of a plan tends to make it bigger. Hold the scope cap:
- When a fix would push the total past the effort target, rule it down to the conservative bound (an upper bound,
  a hard stop, "measure later") instead of accepting the growth. Missing beats wrong: withhold or label a
  measurement rather than add machinery to rescue it.
- When the same mechanism is refined round after round (accounting, sampling, caps), stop the loop: prescribe the
  simplest conservative version and hold one final bounded round. List exactly what to fix and re-check those
  items and the claims or checks they affect. Recommend approval only after the required fixes are verified
  and no blocking finding remains or is newly discovered. Record non-blocking findings as known limits.
- A requester asking "how much of this is worth doing now?" is the signal the cap was missed.
For a plan that must survive hard scrutiny ("challenge every claim"), run a parallel panel with one lens per
reviewer: [references/adversarial-plan-review.md](references/adversarial-plan-review.md).

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
