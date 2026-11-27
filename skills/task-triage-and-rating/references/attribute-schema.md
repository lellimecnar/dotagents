# Task attributes (fixed schema; every task gets all fields)

Map these onto the task tool's native fields (labels, priority, due date, relations) as the household's
local skill describes, and filter with fields rather than by reading descriptions. A typical label
encoding: `size:M`, `diff:med`, `load:high`, `energy:low`, `ctx:computer`, `due:hard`, `batch:online-forms`,
`deleg:y`; description first lines `postponed: 2` / `src: ...`.

size (padded per rule 8; time for the real work, excluding travel unless it's an errand)
- XS: 5 min or less ('Draft a yes to the RSVP', 'Put the form in the car')
- S: 5-20 min ('Fill a short online form', 'Check the pantry list')
- M: 20-60 min ('Clear the workbench', 'Compare two quotes')
- L: 1-2.5 h ('Assemble the shelf', 'Reconcile the quarterly budget')
- XL: over 2.5 h or multi-session. MUST be split into pieces of L or smaller before
  scheduling. Until it's split, list it only under Top priorities with `note:split needed`
  and a proposed split.
- Unknown? Size it from similar completed tasks, the source, or the person's own estimate on
  record. If none exists, write `size:unknown` and `note:needs sizing`, so it is not scheduled
  until sized.

diff (difficulty = skill + uncertainty: does the person know how, and how much is unknown?)
- low: done it before, steps known. med: some unknowns, or a skill they rarely use.
- high: never done, unclear steps, research needed, or depends on outside parties.

load (cognitive load = focus and working memory needed while doing it)
- low: autopilot, interruptible (unload dishwasher, sort mail).
- med: some attention, easy to resume after an interruption (fill a known form).
- high: sustained focus, many details held at once (taxes, insurance claim, writing a dispute).

energy (physical)
- low: seated or light. med: on feet, some carrying. high: heavy lifting, sweaty, hard outdoor work.

ctx (where it must happen; exactly one primary)
- home, <specific area of the home>, computer (desk/office, needs a real screen), online (can be done on phone
  or any device, anywhere), errand (must leave the house; add the place), outside (yard).

due
- hard YYYY-MM-DD: a real external consequence (fee, legal, lost slot, someone else's date).
- soft YYYY-MM-DD: a self-chosen or preferred date. none: no date. Never invent hard dates.

prio (ranking rule; apply top-down, first match wins)
- P1: overdue hard deadline, OR hard deadline within 3 days, OR blocks someone else today,
  OR a safety, health, legal or financial-penalty risk.
- P2: hard deadline in 4-14 days, OR blocks another task or person, OR postponed 3+ times
  with a real consequence, OR the user explicitly flagged it important.
- P3: soft deadline within 14 days, or a meaningful improvement with no deadline.
- P4: nice-to-have / someday.
- Ties within a level: earlier deadline, then bigger consequence, then smaller size
  (quick progress builds momentum [8]).
- Urgent language in an email ('ACT NOW') does not raise priority on its own (rule 16).

blocked_by: none | task id | person/org + what is awaited | info missing | phone-only.
postponed: integer. Add 1 every time a planned task is rescheduled or rolls over undone.
batch: short slug shared by tasks to do together (online-forms, garage, errands-north,
  hardware-store, desk-admin). Prefer the same ctx and similar load.
deleg: n | y:<kind>. Kinds: household member; service provider (costs money, so the user
  decides); assistant/agent (research, drafting, online lookup); the sender (ask them).
src: manual | email|sender|date|subject | agent|<name>|date.

## Grouping and sorting

On request, group or sort by any attribute: size, diff, load, energy, ctx, due, prio, batch,
deleg, postponed, blocked_by, src. Show group headers with counts, and use the default
ordering inside each group. Combined filters are common ('ctx:home energy:low size<=S').

Default ordering for planning:
1. prio (P1 to P4). 2. due (hard, then soft, then none; earlier first).
3. Blocked tasks never appear in the actionable list. 4. postponed (higher first within the
same prio). 5. size (smaller first). Then cluster into batches, as long as that doesn't push a
P1 later.

Finding what fits right now: filter by available ctx, then energy <= current energy, then
load <= current focus, then size <= available time (padded), then apply the default order.
