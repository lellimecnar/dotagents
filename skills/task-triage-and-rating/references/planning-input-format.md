# Planning input format

Always include every section, in this order, with these exact headers. One line per task:
title in quotes first, then `field:value` pairs separated by spaces. An empty section says
`none`. No prose. Add a `note:` only when it changes a scheduling decision (split needed,
needs daylight, needs store hours, depends on someone else's availability, no_restday).
Each task appears once, in the first section that applies (the order below wins). The
exception is New from email, which also lists new items already placed above as id references.

```
PLANNING INPUT <YYYY-MM-DD> window:<start>..<end>
## Hard deadlines
"Submit permit renewal" id:t12 due:hard 2026-11-20 prio:P1 size:S load:med energy:low ctx:online first:"Open city portal, click Renewals" batch:online-forms
## Overdue
"Return library books" id:t07 due:soft 2026-11-05 prio:P3 size:XS ctx:errand batch:errands-north postponed:2 no_restday:true
## Top priorities
"Organize basement shelves" id:t20 prio:P2 size:XL ctx:basement energy:high no_restday:true note:split needed -> t20a(L) t20b(M)
## Quick wins
"Confirm dentist slot online" id:t31 prio:P3 size:XS ctx:online
## Batches
online-forms: t12 t31 total:30m | errands-north: t07 t15 total:50m
## Blocked
"Fix gutter" id:t09 blocked_by:contractor quote (waiting since 2026-11-02) follow_up:2026-11-14
## Chronically postponed
"File warranty claim" id:t04 postponed:4 blocker:unclear first step fix:if-then Tue 09:00 first:"Search email for 'warranty' receipt"
## New from email
"Sign school field-trip form" id:t40 src:Office <office@school.example>|2026-11-10|"Field trip" due:hard 2026-11-14 prio:P1 size:XS ctx:home
```

Field rules: always include id, prio, size and ctx, plus due when present. Include load,
energy, batch, deleg and postponed when known or non-default. Include first only when the first
step is not obvious from the title or the task has been postponed; the scheduler may copy task text
onto the calendar, so titles name the concrete result and never carry an obvious start-up step. Sizes are already
padded. Every chore carries `no_restday:true` when the household has a rest-day rule.

Section definitions:
- Hard deadlines: hard due dates inside the window (not yet passed).
- Overdue: any due date already passed.
- Top priorities: remaining P1/P2 (max 5).
- Quick wins: unblocked XS/S tasks at P3/P4 (max 6).
- Batches: groups of 2+ task ids with summed padded time.
- Blocked: blocked_by is not none; include a follow-up date.
- Chronically postponed: postponed 3+ times, with a likely blocker and a concrete fix.
- New from email: tasks created since the last planning input.

Also put the same data as JSON in the task-completion metadata (if the coordinator reads JSON):
`{"date":"YYYY-MM-DD","window":["start","end"],"sections":{"hard_deadlines":[],
"overdue":[],"top_priorities":[],"quick_wins":[],"batches":[{"name":"","task_ids":[],
"total_min":0}],"blocked":[],"chronically_postponed":[],"new_from_email":[]},
"tasks":{"<id>":{"title":"","size":"S","size_min":[5,20],"diff":"low","load":"med",
"energy":"low","ctx":"online","first":"","due":{"type":"hard","date":"YYYY-MM-DD"},
"prio":"P1","blocked_by":null,"follow_up":null,"postponed":0,"batch":"","deleg":"n",
"no_restday":false,"src":{"type":"email","sender":"","date":"","subject":"",
"thread_id":""},"note":""}}}`
Sections hold task ids; empty sections are `[]`. The text and JSON must agree, so build the
JSON first and generate the text from it.
