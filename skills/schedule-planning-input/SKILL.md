---
name: schedule-planning-input
description: Use when a coordinator asks you for input to a person's schedule.
metadata:
  version: "0.1.0"
  status: draft
  owner: maintainer
---

# Planning input from a specialist

## Purpose
A person's assistant builds their week or day from several specialists' input; a scheduler turns it into time
boxes. You are one of those specialists. Your input is what makes the plan specific to this person: without it the
scheduler can only place generic boxes. Give the scheduler exactly what it needs to put your part of the person's
life on the calendar well, and nothing it would have to throw away.

## When to use
- A coordinator or assistant asks for your planning input for a window of dates.
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
- **Prep the day before**, if any (thaw, charge, buy, lay out), as its own item with its own timing.
Also send:
- **Nothing this window**: say so in one line; it is an answer, and the scheduler plans without you.
- **Decisions**: anything only the person can decide, one line each, with your recommended answer.
- **Conflicts you see** with the fixed events you were given (one line each, with a fix).

## How to work
1. Read the request: the window, the fixed events, and the items the coordinator listed as yours.
2. Research before you answer. Every item, time, amount, dish and status comes from your own tools and memory
   (your plans, your records, the person's stated preferences in your domain) or from the agent that holds it (ask
   it; your card resumes when it answers). Never invent, assume or estimate one, and never write "assumed",
   "unknown" or "probably" in a line: an item resting on a fact no source holds stays out of your items and goes
   under Decisions as the question only the person can answer, saying what you checked.
3. Plan for these days as they are. The request's context (a break or holiday, food already on hand, guests, what
   the person said about the days) rules items in or out, and so does what is already done: check the state of
   each item before proposing its next step, and never propose work the person finished or said they will not do
   in the window.
4. Respect the household's standing limits stated in the request or in memory (rest days, never scheduling a
   phone call for someone who avoids them, spending decisions belonging to a parent).
5. Size items honestly; the scheduler pads and fits them, so do not pad twice or trim to fit.
6. Keep it short: one line per item, no history, no explanation the scheduler does not need, no private detail the
   coordinator did not ask for (the request may be visible to other agents).

## Output format
```
PLANNING INPUT <domain> <start>..<end>
- <date or by:<date>> | <what> | <duration> | <when-constraint and why> | <must|option> | depends: <x|none>
Prep:
- <date> | <what> | <duration> | <timing>
Nothing this window: <yes|no>
Decisions:
- <question> -> recommend: <answer>
Conflicts:
- <conflict> -> fix: <fix>
```
Write `none` for an empty section. If the coordinator reads machine data, put the same items as JSON in your
completion metadata.

## Pitfalls
- Sending your whole backlog instead of what belongs in the window.
- Copying a generic routine instead of this person's actual plan in your domain.
- Tiny first steps or hedges that end up on the calendar as filler.
- Deciding for the person (money, priorities) instead of listing the decision with a recommendation.
- Silence: an unanswered request leaves the plan waiting on you.
- Filling a gap with an assumption: the scheduler writes it on the calendar as a fact.
- Retaining your input as a memory fact: a planned item is not something that happened.
