---
name: daily-brief
description: Use when writing a person's morning brief or building today's plan for them.
metadata:
  version: "0.1.0"
  status: draft
  owner: maintainer
---

# Daily brief

## Purpose
Give a person one short message each morning (or the evening before) that tells them what today
holds and what to do first, built from live calendar and task data, with start prompts that fire
at the right time and never double up.

## When to use
- A scheduled "morning brief" or "build my day" job.
- The person asks "what's today look like?" or "build my day".
- Not for rebuilding the week (that is the planner's job; see `capacity-first-scheduling`).

## Method
1. **Check the real date and weekday first** (run a clock command). Session-start dates and UTC can
   be a day off at night; a wrong weekday breaks rest-day rules and trust.
2. **Read, don't rewrite, the inputs:** today's calendar events (hard times, conflicts), the
   person's task lists, yesterday's slipped items, the planner's plan for today if one exists.
3. **Build or refresh today's blocks** on the calendar the assistant owns (see
   `owned-calendar-stewardship`): list today's events first and update your own blocks in place
   instead of adding new ones; never stack overlapping duplicates.
4. **Pick-one menus:** where a block holds options, list them in the event description by outcome.
5. **Start prompts** (push notifications or reminders): first list what is already scheduled and
   delete stale prompts so nothing fires twice; then schedule the new ones.
6. **Write the brief:**
   - First event and leave-by times; anything to bring.
   - Mandatory items for today, then 1-3 suggestions.
   - Decisions needed (one line each, with your recommendation).
   - Who or what is away or different today, if it changes the routine.
   Keep it under the channel's comfortable length (a few hundred words at most), plain, no lecture.
7. **Deliver it as your reply on the person's chat channel**, not on a shared task board, which
   other agents can read.
8. **Use facts already given** (memory, notes, earlier messages) instead of re-asking; never
   invent an item, event or time.

## Pitfalls
- Building from memory instead of a fresh calendar read; trusting a stale session date.
- Duplicate blocks or duplicate notifications from a previous run.
- Long briefs, many options, or advice nobody asked for.
- Posting the brief where other agents can read private details.
- Re-asking questions the person already answered.

## Verification
- The brief's events match a fresh calendar listing for the correct local date.
- Scheduled prompts were listed after changes: one per start, none stale.
- Every option in a menu is a real task named by its result.

## Household specifics
The agent's own local skill supplies the job schedule, calendars and task lists to read, the
notification service and its credentials location, standing routine rules (meal times, clock
format, recurring commitments, what is never an event), the brief's channel and length limit, and
who to ask when write access fails.
