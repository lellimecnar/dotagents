---
name: capacity-first-scheduling
description: Use when building or repairing a week or day of time boxes, ADHD-aware.
metadata:
  version: "0.1.2"
  status: draft
  owner: maintainer
---

# Capacity-first scheduling (ADHD-aware time boxing)

## Purpose
Turn a planning brief (fixed events, prioritized tasks, money deadlines, other constraints) into
time boxes on a person's calendar, and answer everyday "when / can I / fix today" questions. Build
capacity before content, pad estimates, leave real slack, and calibrate the load from what
actually got done. When the person has ADHD the plan works as an external prosthetic for time
sense, not a wish list: Barkley frames ADHD as a problem of doing what you know at the point of
performance, so put time and the next action outside the head, where and when the work happens.

These are principles with reasons, not a rulebook. The person's own data docs (wake/sleep, meals,
focus window, work hours, rest days, calendar conventions) always win; where a principle here
conflicts with them, follow the data doc and note the conflict in your output.

## When to use
- A weekly or daily planning brief arrives (from the person, an assistant, or specialist agents).
- A disruption: sick day, urgent email, new deadline, cancelled event, "I lost the morning".
- Chat: "when should I do X", "I'm behind, fix today", "can I fit Y", "move Z".
- Not for: deciding task priority (the brief or the task manager owns it), paying bills, or
  sending messages. If a task needs an action the person has asked not to be scheduled (for
  example a phone call) or a human decision, list it under "Needs human" instead of boxing it.

## Method

### 1. Build capacity before content
1. Lay down fixed events in the window with travel, prep and a leave-by time.
2. Lay down anchors and protected time from the data docs: meals as real boxes, medication,
   family time, routines the person already does.
3. What is left is available time. Count focus hours honestly: most adults get 3-4 real focus
   hours a day; with ADHD plan for fewer, inside the person's peak window.
4. Capacity = available x slack ratio (book at most 60-70%) x velocity factor (section 4).
   Queues and variable-duration work collapse near 100% utilization; the 60-70% figure is practice,
   not a trial result, so calibrate it from velocity.

### 2. Principles (with why)
**Time and estimates**
- **Pad every estimate.** Use the person's own calibration factor per task kind once 3+
  completions exist (median actual/estimate, bounded 0.7-3). Until then 1.5x for medium-plus or
  unfamiliar work, 1.25x for small routine work. Planning fallacy: thesis students predicted 34
  days and took 55; ADHD adds measurable timing deficits. No padding factor has an evidence base;
  the personal one is what matters.
- **Plan on a 15-minute grid.** Fake precision (09:07-09:52) produces "already behind" by 10:00.
- **Externalize time.** Every box has a visible start, end and category; its description starts
  with the first physical action ("open doc X"), not the goal.

**Transitions and slack**
- **Transition buffers:** ~10 min between context changes, 15 when place or mode changes; travel
  plus 10-20 min before off-site events. Attention residue degrades the next task, worst when the
  prior one is unfinished; transitions are a known ADHD failure point.
- **Free time is not a box.** Slack is empty calendar space. "Rest" or "free time" boxes get
  ignored or become guilt. A specific restorative activity the person chose (walk, nap, gym) may
  be a box with a first action.
- **One overflow box per day**, late, 20-45 min, to absorb spillover and replanning.

**Load, energy, order**
- **Hardest high-load task in the focus window** (the person's stated window; default the first
  2-3 hours after the morning routine). Chronotype effects are real but modest; personal history
  beats theory.
- **Low-load work in the post-lunch dip** (~1-3 h after the midday meal).
- **Never stack two high-load boxes**; put a low-load, physical or break box between. Do not
  justify this with "decision fatigue": ego depletion is tiny in multi-lab replications (d about
  0.1). The real reasons are attention limits, time-blind overrun and residue.
- **Start with an easy win** (5-15 min) because initiation is the bottleneck. Coach consensus,
  weak formal evidence.
- **Cap box length:** high-load 25-50 min of work with breaks, max 90 min; low-load max 120 min.
  Split longer tasks into parts in different slots.
- **Vary the day** (desk / physical / out of house / social); interest and novelty drive ADHD
  engagement, monotony kills it.
- **Pair dreaded tasks** with a reward or a body double when available (evidence mostly
  self-report; an option, not a guarantee).

**Placement and selection**
- **Anchor fixed events first**, then fit tasks into gaps.
- **Deadline-first, then priority:** hard deadline in the horizon (work back, at least one day of
  margin) -> money due -> brief priority -> postponed count -> age. Mark deadline, bill and other
  mandatory items as mandatory and everything else as suggested, in the household's format.
- **Spread neglected tasks:** postponed 2+ gets a guaranteed early slot, at most two per day; at 3+
  schedule only its first 15-25 min step and flag it.
- **Batch** errands by area and same-tool work (admin, forms) into one box with checkboxes.
- **Size admin blocks to the work selected.** Use authorized information to check due dates;
  estimate time for each item and show only the details needed to act. Avoid turning a focused block into a backlog.
  Use freed time for the person’s priorities or keep it as buffer, as agreed.
- **A priority the person names for a day gets a real block** before you reply, taken from
  lower-stakes boxes first.
- **Avoid unnecessary fragmentation.** Group adjacent work when useful and split it when the
  task or the person’s preferences call for it. Match the requested scope of each block.
- **Tasks flow through time.** Order by dependency, daylight (outdoor work), energy and deadlines;
  group related work; leave real gaps. Before writing a day, state in one line why each box sits
  where it does; a box you cannot justify is scattered.
- **Check actual attendance constraints.** Overlap a passive commitment only when its location,
  attention, and coverage requirements are compatible with the other block.
- **Cap any single project** at ~40% of the week's focus hours unless it has a deadline; boost
  tasks untouched for 5+ days; track hour targets per category if the person keeps them.
- **Respect the household's rest-day and money rules** (for example a day with no chores, or bill
  tasks only after payday). Move work earlier, never later, around them.
- **Write if-then fallbacks** into the riskiest box each day ("If <obstacle>, then <smaller
  version / swap slot>"). Implementation intentions show medium-to-large effects across 94 studies;
  ADHD evidence is mostly in children.
- **Name the minimum-viable day:** if only the mandatory items happen, the day still counts.
- **Fewer boxes when completion drops.** A plan that gets done beats a full plan that doesn't.

### 3. Algorithms
**Weekly (rough boxes):** read data docs, calendar and velocity -> compute daily available and
capacity -> pad and split tasks -> place mandatory items first (latest safe day minus margin,
never the deadline day for medium-plus tasks), then neglected tasks, then the rest by priority
while day load <= capacity -> place boxes by load and energy, one overflow box per day -> anything
that does not fit goes to Dropped/Deferred with a reason. Weekly boxes are themes ("Deep work:
proposal", "Errands"), not checklists. Never overbook silently. Check authorized calendars
within the agreed horizon. Mirror commitments only when requested and authorized, to the designated calendar, so any proposed day
(a follow-up appointment, say) can be judged against a real week instead of a blank one.

**Daily (final tomorrow, rough refresh after it):** finalize tomorrow and refresh later drafts
only as far ahead as the agreed planning cadence calls for. For tomorrow: re-read the calendar and today's completion -> update velocity ->
carry over unfinished mandatory items only (suggested ones return to the pool) -> exact 15-min
boundaries, transitions, easy win, low load after lunch, overflow box -> 1-3 suggestions per box
with mandatory items separated; padded checkbox minutes must fit the box -> if-then lines on the
1-2 riskiest boxes, leave-by times and event details (address, what to bring).

**Midweek replan:** smallest change that works. Freed time goes to slack first. Insert the new
item by deadline/priority, displace suggested items first, move (never silently drop) mandatory
items, and flag anything pushed past its margin as at risk. Rewrite only affected days; churn
erodes trust. If the week is impossible, say so first and list the smallest set of items to move;
let the person or coordinator decide. Sick/low-energy day: mandatory only, boxes halved, excluded
from velocity.

**What-if check before writing:** if the first box starts 45 min late, what absorbs it? If the
hardest task takes 2x, which suggested item drops? If a fixed event runs long, is the next buffer
enough? If any answer is "nothing", adjust.

### 4. Velocity (load calibration)
Record planned vs completed padded minutes per day; completion ratio r = sum(completed) /
sum(planned) over the last ~10 planned days (min 5; exclude sick, travel, emergency and rest days).
Velocity factor v starts at 1.0, bounded 0.4-1.0: r < 0.6 -> v x 0.8; 0.6-0.8 -> v x 0.9; 0.8-0.95
-> hold; >= 0.95 for 5+ days -> min(1.0, v x 1.1). Change v at most weekly. Mandatory items are
always placed; v trims suggested items. Report r and v as information, never as failure. This is
the sprint "yesterday's weather" rule. Detail: `references/velocity-and-output.md`.

### 5. Everyday chat
One decisive recommendation with the slot and the reason in one line; check the live calendar
first. "I'm behind, fix today" -> keep mandatory items, defer suggested ones, shrink the next box
to its first step, restore one buffer, rewrite the rest, reply "Done: kept A and B, moved C to
Thu, dropped D. Next: 14:15, start A with <first action>." "Can I fit Y?" -> pad Y, check capacity,
lead with yes or no. Do not lecture about ADHD or list principles.

## Pitfalls
- Overpacking to 100% of available time; ignoring transitions and travel.
- Boxing "free time" or empty "rest"; scheduling to the minute.
- Re-issuing the same load after a low-completion week; rolling every unfinished task forward.
- Same-kind marathons; hard work in the post-lunch dip; back-to-back high-load boxes.
- Hiding drops instead of listing them with reasons.
- Editing events the planner did not create, or that the person edited (treat those as locked).
- If lock information is missing or inaccessible, treat existing events as potentially locked.
  Offer a conditional plan without moving them and request a minimal lock list through an approved channel.
  Use explicit event ownership and lock metadata when available; leave events untouched if provenance is unclear.
- Defining calendar mechanics (naming, colors, checkbox syntax) in this skill instead of the
  household's conventions doc; if that doc is missing, stop before writing and report it.
- Over-trusting chronotype or decision-fatigue science; personal history beats both.
- Paragraph descriptions on boxes; keep them to short glanceable lines.
- Boxing routine reminders without agreement; include only the reminders and level of detail
  the person wants on that calendar.
- Two tools writing boxes for the same range (for example a deterministic planner plugin and this
  method): pick one writer and use the other as a cross-check.

## Verification
- Booked load <= capacity for every day; every day has an overflow box and real empty slack.
- Every mandatory item has a box before its latest safe day, or is listed as at risk.
- Everything that did not fit appears under Dropped/Deferred with a reason.
- After writing, re-list the calendar and confirm each event landed at the intended local time;
  never claim an event was written unless the write call succeeded.

## Household specifics
This skill is the method only. The agent's own local skill or the household's shared data docs
supply the person's fixed times, focus window, meal and rest-day rules, hour targets, which
actions must never be scheduled, calendar conventions (names, colors, mandatory/suggested
markers, outline vs solid), where velocity is stored, who sends briefs, and the exact output
format the coordinator expects.

## References
- `references/velocity-and-output.md`: velocity detail and a generic output template.
- `references/evidence-and-sources.md`: the evidence behind each principle, with strength notes.
