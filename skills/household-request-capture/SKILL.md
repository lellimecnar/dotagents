---
name: household-request-capture
description: Use when someone asks their assistant to track a chore, home item or project.
metadata:
  version: "0.1.0"
  status: draft
  owner: maintainer
---

# Capturing household requests

## Purpose
Turn what a person says in chat into the right thing: a saved fact, a task, or a self-contained
request to a specialist agent, and relay specialists' answers accurately and briefly. The assistant
keeps the person's list complete, never fills gaps with invented steps, and never makes them repeat
themselves.

## When to use
- The person states a to-do, a chore, something to track or reorder, or a home project step.
- They share facts, routines, recipes, photos or measurements.
- A request needs research by a specialist (home systems, organizing, building, finance).
- Planning, sell-stuff or "find that old order" asks.

## Method

### 1. Request or information?
- Facts, routines and recipes the person shares are for the record, not a prompt for coaching.
  Save them to notes and memory (health items as the plain operational constraint only), reply in
  one line, no tips unless asked. Unsolicited advice annoys.
- Check the real date before naming a weekday.

### 2. Capture
1. Every stated to-do becomes a task immediately. Ideas are not tasks.
2. Respect the household's date rules (for example chore-type tasks never on a rest day).
3. "Track / reorder X" -> a dated check task with the interval taken from order history or the
   product's stated life; ask only if neither exists. Pricing yes, buying never.

### 3. Specialists
- Research that needs a specialist goes to that specialist as a self-contained task: no personal
  details, no credentials, and the person's own words on what they want done (a setup the person
  asked for proceeds; purchases and irreversible steps still need their yes). Private matters
  never go on a shared board.
- Also create the person's follow-up task (setup/install) so their list stays complete.
- Name the shared notes file for that area in the request and add only what is new; list what the
  person already said (dimensions, settled decisions) and tell the specialist not to re-ask it.
- Ask for the level of detail the person needs to act in the first request: an executable plan at
  the granularity of the request, not an overview (for organizing, a per-unit plan that works even
  when the space is already full). Unknown facts -> the specialist asks the shortest questions
  instead of guessing.
- Relay the result in about 3 lines: recommendation, main risk, one yes/no ask. Credentials are
  always typed by the person, never relayed.

### 4. Sanity-check before relaying
Specialists reading notes can invent labels, tasks or locations, or re-ask settled decisions.
Check output against what the person told you. Fix errors in the notes file first (a dated
"Corrections" section that supersedes earlier plans, in the person's own words), then point the
specialist to it. Never relay wrong claims verbatim.

### 5. Piecemeal answers and saving results
- When the person reveals details in pieces, append each answer to the area's notes under a
  heading in their words, then send the specialist one task naming that section; corrections that
  arrive mid-task go in as a comment on that task. When complete, ask for one consolidated plan
  that replaces earlier versions.
- If a specialist cannot save its result where the person can see it, save a summary yourself as a
  new versioned notes file, marked as a summary; don't claim a full attachment was saved.
- Items being sold or moved out are recorded as leaving; the plan is rebuilt around what stays.
- Photos: describe literally; ask what stage or date they show if unclear; record it.

### 6. Planning and project asks
- Never fill a plan with generic steps ("measure", "mark studs") when the real state is unknown.
  Read the project notes first; if the next step or blockers aren't recorded, ask one short
  question. Invented filler destroys trust.
- When the person states a build order or prerequisite chain, record it at once with every side
  item they mention.

### 7. Lookups
- Old orders: an order number alone rarely finds the item list. Search mail by vendor domain and a
  date range around the order, then open the separate fulfillment or "sales order" email (often from
  the manufacturer, not the store). Open candidates rather than asking whether to keep digging.
  Fetch large messages in small slices so the item list is visible.
- Re-order / "still sold?": search the model, read the vendor page, quote its base price (not a
  configured price), say plainly what can't be priced, and offer a drafted quote-request email or
  web form for the person to send when their channel preferences exclude calls.
- If a tool has no access to something (for example photo libraries), say so and ask the person
  to send it.
- Expired account authorization: tell the person how to reconnect, retry after they confirm.

## Pitfalls
- Coaching when the person only shared information.
- Losing a stated to-do because it was mentioned in passing.
- Personal details or credentials in a shared specialist task.
- Relaying a specialist's invented claims; re-asking settled questions.
- Generic filler plans; buying instead of pricing.

## Verification
- Every to-do from the conversation exists as a task (and a follow-up task for specialist work).
- Every specialist request is self-contained and lists what is already known.
- Relayed answers were checked against the notes and the person's own words.

## Household specifics
The agent's own local skill supplies the task tool and lists, shared notes folder and file names,
which specialist owns which area, board conventions, private-delegation route, date rules, known
catalogs or files, and how to reconnect expired accounts.
