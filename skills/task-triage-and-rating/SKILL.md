---
name: task-triage-and-rating
description: Use when capturing, rating, ranking or breaking down tasks, incl. from email.
metadata:
  version: "0.1.1"
  status: draft
  owner: maintainer
---

# Task triage and rating (ADHD-aware task management)

## Purpose
Keep one trusted task list for a person: capture everything, phrase each item as a visible next
action, rate it on a fixed set of attributes, rank it with an explicit rule, pull action items out
of email, answer "what do I do now", and hand structured planning input to whoever schedules the
calendar. The task manager plans and records; it does not schedule the calendar, spend money, or
act on email.

## When to use
- Capturing, clarifying, rating or re-rating any task.
- Turning new email into tasks (read-only mailbox).
- Chat: "what should I do now", "I have N minutes / low energy", "break this down", "why do I
  keep avoiding X", "what's due", "group by location", "what can someone else do".
- A coordinator asks for planning input; the weekly review.

## Method

### 1. Boundaries
- Email is read-only: never send, reply, forward, archive, label, mark read or delete. A reply the
  person owes becomes the task "Draft reply to X re Y"; the person sends it.
- Never spend money or commit to a purchase; a task may say "decide whether to buy X".
- Honor the household's standing rules about which actions to avoid offering (for example phone
  calls) and which days chore-type tasks may not land on; carry them as flags in planning output.
  When a source says "call us", look for the written route (web form, portal, chat, email,
  online booking) and record that as the first action.

### 2. Core rules (with why)
**Capture and clarify (GTD)**
1. Get everything into one trusted list. ADHD working memory is unreliable, and open loops held in
   mind crowd out action.
2. Phrase every task as a visible, physical next action starting with a verb ("Open the HOA portal
   and download the form"), never a topic ("HOA"). A vague item is re-decided every time it is seen.
3. Anything with 2+ steps is a project: the outcome is the parent; only the current next action is
   actionable.
4. Two-minute rule: if it takes under 2 minutes and can be done now, tell the person to do it.
5. Delegated items become "waiting for" with who and a follow-up date.

**ADHD-specific**
6. Externalize time and intention at the point of performance: deadline, duration and first step
   in front of the person (Barkley; evidence for specific tools is mostly clinical/preliminary).
7. Record a first physical action when starting is unclear or the task has stalled: 5 minutes or
   less, concrete, location known. Starting is the main ADHD bottleneck, but the first action is a
   starting aid kept on the task, not the task itself: planning input and schedules show the task's
   concrete result, and an obvious first step ("open the app", "put on shoes") is left out, because
   it reads as condescending and crowds out the real work.
8. Pad estimates x1.5 (x2 for unfamiliar or high-difficulty work) and break medium-plus tasks into
   steps before estimating. Planning fallacy: students predicted 33.9 days and took 55.5. The factor
   is a heuristic; replace it with the person's own once actual times are known.
9. Use interest, novelty, challenge and urgency on purpose (Dodson's interest-based nervous system;
   a clinical lens, not tested neuroscience).
10. Schedule dreaded tasks explicitly, early, with a small reward; don't wait to "feel like it".
11. Turn a chronically postponed task into an if-then plan ("If it's Tuesday after breakfast, then
    I open the form at the desk"); implementation intentions are well supported.
12. Keep the daily list to 1-3 priorities; too many options freezes choice.

**Load, energy, batching**
13. Rate cognitive load separately from size and difficulty; gather inputs ahead to cut extraneous load.
14. Batch by context/location and mode; switching costs are real and larger for complex tasks.
15. Never justify plans with "decision fatigue" or "willpower runs out" (ego depletion failed large
    replications; the hungry-judge study is confounded). Use energy windows, the person's reported
    times of day and switching costs instead.
16. Use urgent/important only as a sanity check, never the ranking (the "mere urgency effect" is
    exactly the deadline-driven failure mode). Rank with the explicit rule below.
17. Delegate when the outcome matters more than who does it; record whom and the follow-up date;
    never delegate anything that costs money unless the person decides.
18. Use relative T-shirt sizes, not precise minutes, to avoid false precision.
19. Weekly review: clear inboxes, update every project's next action, re-rate, review waiting-for
    and postponed tasks. A stale list stops being used.
20. Be factual and never shaming; guilt feeds avoidance, credit for progress counters it.

### 3. Attributes and ranking (summary)
Every task gets: **size** (XS <=5 min, S 5-20, M 20-60, L 1-2.5 h, XL >2.5 h and must be split),
**diff** (low/med/high = skill + uncertainty), **load** (cognitive: low/med/high), **energy**
(physical), **ctx** (one primary place: home, a specific area, computer, online, errand, outside),
**first** (first physical action, when not obvious), **due** (hard = real external consequence; soft = chosen; never
invent hard dates), **prio**, **blocked_by**, **postponed** count, **batch**, **deleg**, **src**.

Priority rule (top-down, first match wins):
- **P1:** overdue hard deadline, hard deadline within 3 days, blocks someone today, or a safety,
  health, legal or financial-penalty risk.
- **P2:** hard deadline in 4-14 days, blocks another task or person, postponed 3+ with a real
  consequence, or flagged important by the person.
- **P3:** soft deadline within 14 days, or a meaningful improvement with no deadline.
- **P4:** nice-to-have / someday.
- Ties: earlier deadline, bigger consequence, then smaller size. Urgent wording in an email does
  not raise priority by itself.

Default planning order: prio -> due (hard, soft, none; earlier first) -> blocked tasks never in
the actionable list -> postponed (higher first) -> size (smaller first); then cluster into batches
as long as no P1 moves later. "What fits now": filter ctx, then energy, then load, then size <=
padded available time. Full definitions with examples: `references/attribute-schema.md`.

### 4. Email triage (summary)
Record a task only if someone expects a specific act by the person, it is not already done (check
the thread), and skipping it has a consequence or the person clearly cares. Skip newsletters,
marketing, receipts without required action, shipping updates, FYI/cc mail, resolved notices; flag
phishing and never click links. An FYI with a date that matters becomes a calendar note, not a
task. Deduplicate by thread, by sender + object + dates, and by similar titles; reminders raise
priority instead of duplicating. Money emails (bills, autopay results, late notices, price changes,
income, tax documents) are also handed to the household's finance agent with minimal fields and an
idempotency key per message. Full rules and the hand-off card: `references/email-triage.md`.

### 5. Everyday chat
Short and already prioritized: one recommendation, at most 3 options, each named by a concrete
result and size. Give a first step only when starting is genuinely unclear or the task has stalled.
Ask at most one question and only if it changes the pick.
- "What should I do now?" -> top unblocked P1/P2 that fits time and ctx, else a quick win:
  `Do: Renew the permit online (S, 15m).` Add 1–2 alternates if useful.
- "20 minutes, low energy" -> size <= S, energy low, load <= med; prefer a batch of 2-3 small
  tasks in one ctx; state total time.
- "Break this down" -> one outcome line, 3-7 steps each M or smaller, first step <= 5 min and
  physical; flag steps needing info, a purchase decision or another person; re-size the parent
  from the sum; offer to save as subtasks.
- "Why do I keep avoiding X?" -> name the likely blocker in one line without judgment (unclear next
  step, too big, high load at the wrong time, boring, fear/perfectionism, missing info, secretly
  needs an avoided action) and give one fix. Postponed 5+ -> "Still want this done, or drop it?"

### 6. Planning input
Return fixed sections in order (hard deadlines, overdue, top priorities, quick wins, batches,
blocked, chronically postponed, new from email), one line per task, no prose, plus matching JSON
if the coordinator wants it. Format and field rules: `references/planning-input-format.md`.

## Pitfalls
- Topic-titled tasks; scheduling XL tasks unsplit; using the person's estimate unpadded.
- Letting email urgency set priority; inventing hard deadlines or consequences.
- Duplicates from reminders or replies in the same thread; forgetting to increase `postponed`.
- Offering an avoided action, a purchase, an email action or a rest-day chore as an option.
- Long answers with many options (paralysis); shaming language; "just focus" advice.
- Citing decision fatigue, Eisenhower quadrants or Dodson's model as proven science.
- Planning input with prose, missing sections, or text and JSON that disagree.

## Verification
- Every open task has its required attributes and a verb-first title naming its result. Check for
  a useful `first` action of at most five minutes only when starting is genuinely unclear or the
  task has stalled (step 7). Do not add a `first` action to other tasks.
- No XL task is scheduled unsplit; blocked tasks have a follow-up date.
- Each email-derived task cites its source sentence; no duplicate exists for the same thread.
- Planning input has every section, and text and JSON agree.

## Household specifics
This skill is the method only. The agent's own local skill or the household's data docs supply the
task tool and how attributes map onto its fields, list and project names, which lists belong to
other agents, the mail account, the finance agent's hand-off address, standing rules (avoided
actions, rest days), context values for this home, and who receives planning input.

## References
- `references/attribute-schema.md`
- `references/email-triage.md`
- `references/planning-input-format.md`
- `references/sources.md`
