---
name: schedule-planning-input
description: Use when a coordinator asks for schedule input or a draft review.
metadata:
  version: "0.2.1"
  status: draft
  owner: maintainer
---

# Planning input from a specialist

## Purpose
A person's assistant builds their week or day from several specialists' input; a scheduler turns it into time
boxes, and each specialist then reviews the draft in its own domain. You are one of those specialists. Your input is
what makes the plan specific to this person: without it the scheduler can only place generic boxes. Give the
scheduler exactly what it needs to put your part of the person's life on the calendar well, and nothing it would
have to throw away.

## When to use
- A request for your planning input for a window of dates: input mode below.
- A request to review a draft schedule: review mode below.
- Not for answering the person directly, and not for writing a calendar (the person's assistant does that).

## What to send (your domain only)
For each item from your domain that belongs in the window:
- **What**: the real task or session, named by its concrete result ("hang the stair garland", "30-minute treadmill
  intervals", "make a dozen hard-boiled eggs for the week"). Never a start-up micro-step ("open the app", "put on
  shoes") and never a hedge ("this is a start, not completion"): the scheduler copies your words onto the calendar.
- **When it fits**: the day or the latest safe day, the time-of-day constraint and why (daylight, store hours, before
  a meal, while someone is home, after a prerequisite), and how long, as a realistic duration.
- **Depends on**: what must happen first (another task, a delivery, a decision, a person).
- **Must or option**: must = a real deadline or consequence; option = a good use of time if it fits.
- **Doer**: the person (or the person with the kids), or the agent that should do it. Research, comparisons and
  integration checks are agent work: create a task for the agent that owns that work yourself and list it under "Agent work", never as the
  person's time.
- **State**: what is already done and what is left, read from your own records or the context you were given. A
  state you cannot read, you ask for (the person's assistant holds what the person said and checked off); never write "unknown". Work
  planned before the window that has not happened yet is `not started`; an item that needs it lists it under
  `depends:`.
- **Source**: where you read the item, its timing and its state (your system, a record, the context you were given, an
  agent's answer).
- **Prep the day before**, if any (thaw, charge, buy, lay out), as its own item with its own timing.
Also send:
- **Nothing this window**: say so in one line; it is an answer, and the scheduler plans without you.
- **Decisions**: anything only the person can decide, one line each, with your recommended answer and what you
  checked. Family logistics (who goes, drives or takes whom) are never a decision: who attends a booked event follows
  from the booking, and the rest belongs to whoever coordinates the family's logistics.
- **Conflicts you see** with the fixed events or the context you were given (one line each, with a fix).

## How to work (input mode)
1. Read the request and any context linked to it: the window, the context (what kind of days they are, who is home, what the
   person said, what is done), and any facts the coordinator gave for your domain.
2. Research before you answer. Find your own items in your own system; the coordinator does not pick them for you.
   Every item, time, amount, dish and status comes from your own tools and memory (your plans, your records, the
   person's stated preferences in your domain) or from the agent that holds it (ask it with a dependency task; your
   task resumes when it answers). Never invent, assume or estimate one (durations are sized as in step 5), and never write "assumed", "unknown" or
   "probably" in a line: an item resting on a fact no source holds stays out of your items and goes under Decisions
   as the question only the person can answer, saying what you checked. Never ask another agent to put the
   question to the person for you: an agent answers only from what it already holds, and a fact only the person
   holds goes straight under Decisions.
3. Plan for these days as they are. The context (a break or holiday, food already on hand, guests, what the person
   said about the days) rules items in or out, and so does what is already done: check the state of each item before
   proposing its next step, and never propose work the person finished or said they will not do in the window.
4. Respect the household's standing limits stated in the request or in memory (rest days, never scheduling a phone
   call for someone who avoids them, spending decisions belonging to a parent).
5. Size items honestly; the scheduler pads and fits them, so do not pad twice or trim to fit.
6. Keep it short: one line per item with one reason and its source; no history; no private detail (other agents may
   see the request).

## Output format (input mode)
```
PLANNING INPUT <domain> <start>..<end>
- <date|by:date> | <what, by its result> | <duration> | <when + why> | <must|option> | depends: <x|none>
  | doer: <person|person+kids|agent:<name>> | state: <not started|done: …; left: …> | source: <where read>
Prep:
- <date> | <what> | <duration> | <timing> | source: <where read>
Agent work (not for the person's calendar):
- <item> -> <agent> task <id>
Nothing this window: <yes|no>
Decisions:
- <owner-only question> -> recommend: <answer> (checked: <where>)
Conflicts:
- <with a given fixed event or the context> -> fix: <fix>
```
Write `none` for an empty section. A line that cannot name a source is left out. If the coordinator reads machine
data, put the same items as JSON in your completion metadata.

## Review mode (a request to review a draft)
Read the draft you were given (usually the parent task's result). Check only your domain: every line you supplied, every change
the draft lists against your input (compare with what you sent if it lists none), and any box that breaks your domain's facts or limits (a cooked
meal on a leftovers day, exercise against the sleep or bedtime pattern, a payment line with no bill due). Reply:
```
REVIEW <domain> <dates>: APPROVE | OBJECT
- <date box/line> | problem (domain reason + source) | required fix | must|should
Conflicts with other domains seen: <box> vs <box> -> proposed fix
```
Approve when nothing in your domain is wrong; never re-plan other domains. One pass: answer from the draft and your
own records; an objection names the fix, not a question.

## Pitfalls
- Sending your whole backlog instead of what belongs in the window.
- Copying a generic routine instead of this person's actual plan in your domain.
- Tiny first steps or hedges that end up on the calendar as filler.
- Agent-doable research sent as the person's time instead of as that agent's task.
- Deciding for the person (money, priorities) instead of listing the decision with a recommendation.
- Silence: an unanswered request leaves the plan waiting on you.
- Filling a gap with an assumption: the scheduler writes it on the calendar as a fact.
- Retaining your input or a review as a memory fact: a planned item is not something that happened.
