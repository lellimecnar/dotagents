---
name: owned-calendar-stewardship
description: Use when an assistant owns a person's calendar and must plan, mirror or fix it.
metadata:
  version: "0.1.1"
  status: draft
  owner: maintainer
---

# Owned-calendar stewardship

## Purpose
When a personal assistant owns the calendar a person actually looks at, keep it clean, complete
and current: write only events it can recognize as its own, never fight the person's edits, copy
relevant events from other calendars without duplicates, run planning as a coordinator (specialists
answer, a planner drafts, the assistant applies), and repair the calendar quickly when the person
questions it. The person should never get calendar cleanup chores from their assistant.

## When to use
- Weekly or daily planning runs, midweek changes, full re-plans.
- Mirroring events from family, work or school calendars onto the person's calendar.
- "Schedule a time to...", "Why is X on my schedule?", "X is missing from my schedule".

## Method

### 1. Data, not memory, decides details
Planning facts (fixed times, rest-day rule, meal rules, hour targets), planning rules (priority,
bills, velocity, locks, reminders), calendar conventions (naming, colors, outline vs solid, emoji)
and mirror rules live in short data docs. Read the ones the step names before writing. When the
person changes a fact or rule in chat, record it the same day. In an editable doc, edit it in place. In an
append-only store, write a dated entry that names what it replaces ("From <date>, <new rule>; this
replaces <old rule>"), so later runs can tell which version is current. Shared docs get scheduling
facts only, nothing private. Old memories of a different convention lose to the current doc or the
newest dated entry.

### 2. Recognize your own events
- **Tag every event you write** with a machine-readable last line in the description: owner,
  kind (box / mirror / event / deadline), the start-end times you wrote, and a stable key
  (`box:<date>:<slot>`, `mirror:<source calendar>:<source event id>`). Rewrite the tag in the same
  write whenever you change times.
- **Locks:** a tagged event whose times no longer match its tag, or whose title/description the
  person changed, was edited by them: never change or delete it again and don't "fix" its tag.
  Keep a lock list so later runs skip it. If one copy of an event can show older data than another
  (invite copies), compare each copy with its own tag before calling it a person's edit.
- **Before deleting**, log the event id; delete only IDs read from a fresh listing, never from
  memory. If the person deletes one of your mirrors, add its source to a do-not-bring-back list.
- **Fixed vs flexible:** fixed events are busy; time boxes and background ranges are free/transparent.
  Every event gets an explicit color if the conventions use colors.

### 3. Mirror pass (every planning run)
1. List calendars, then read each calendar the mirror rules include, for the window (small queries:
   one calendar or one day per call, so results are not truncated).
2. For each event that passes the rules, find its mirror by source id AND by title + start time
   (keys drift; duplicates hide under different keys). Create if missing; update if the source
   changed. Keep the copy keyed to the real source id, delete the rest.
3. Delete mirrors whose source is gone, unless locked (then tell the person at check-in).
4. Never touch the source event. Mirror recurring sources as one recurring event with the same
   rule. All-day source events are easy to miss; compare them explicitly.
5. **Loop guard:** skip any source event that carries your tag or has your calendar as organizer
   or guest.
6. When the same event is on two source calendars (an invite), mirror it once.

### 4. Planning as coordinator
1. Check the real date. Read facts and rules. Run the mirror pass.
2. Gather what only you can see (facts only, never raw email text): bill notices, the person's
   open tasks, fixed events and locked boxes, velocity and last period's hours vs targets.
3. Consult specialists in parallel (task manager for ranked tasks with estimates and first actions;
   finance agent for bill status, amount, latest safe day, minutes; any other owner). Keep shared
   task bodies to what each specialist needs.
4. Ask the planner to draft from those answers (every open bill gets its own box).
5. Apply one day at a time in order, from the board's handoffs, not from memory. Validate the
   draft against the household's rules first; if it breaks one, send it back to the planner with
   the exact problem instead of patching it yourself.
6. Write your day: replace your unlocked boxes in place, never duplicate, never touch locked ones;
   re-list the day and fix mismatches. Add event details (address, leave-by, what to bring, links).
7. Write a short "tomorrow" summary for the evening check-in; complete with a one-line summary;
   message the person only if a decision is needed. Never plan without the specialists; if a card
   blocks, answer it if you can, otherwise tell the person in one line what is waiting on whom.

### 5. Ad-hoc requests ("schedule a time to...")
- Check the real date; read the target day fresh.
- Look up real opening hours and put the address in the location.
- Order stops the way the person does them, include travel and put-away time, and plan meals and
  drive time before an errand rather than squeezing it into a gap.
- Rearrange surrounding boxes in the same turn instead of overlapping, and consolidate fragments
  into whole boxes; follow the household's conventions doc for box rules.
- Routines the person states (get-ready windows, bedtimes, deadlines) become their own boxes; move
  colliding boxes and tell the person the new times.
- Before keeping a task box, check it against what the person said is done; delete stale boxes on
  every later day too.

### 6. "Why is X on my schedule?" / "X is missing"
- Open the event and read its description before answering. A stale box is deleted at once, not
  defended: say what it was and that it is gone, in two lines.
- Missing event: search each relevant calendar, then email by keyword, before concluding; a hit
  about a different audience is not the event. If nothing is found, say so and ask for time and
  place. Never invent an event or time. Add the reason when an event changes the household's
  routine.
- A follow-up that is not for tomorrow goes to the task list with no date, never onto a box.

## Pitfalls
- Timezone and clock bugs: pass an explicit timezone, and verify every write by re-listing the day
  in local time before reporting it done.
- Large listings that are truncated or doubled, making later days look missing or duplicated.
- Answering "not mine to delete" or handing the person cleanup chores.
- Accepting invites or setting RSVPs on outline/placeholder events when the conventions forbid it.
- Re-asking questions the person already answered.

## Verification
- Every event you wrote carries a tag matching its current times; no locked event changed.
- No duplicate mirrors (checked by source id and by title + time).
- A fresh listing of each written day matches the plan.

## Household specifics
The agent's own local skill supplies the calendar names and IDs, data-doc names and locations, the
exact tag format, outline/solid method, colors and emoji, mirror rules and exclusions, run
schedule, specialist agents and board conventions, tool quirks and workarounds, and standing
routine rules (rest day, meal times, clock format).
