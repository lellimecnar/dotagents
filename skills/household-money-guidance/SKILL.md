---
name: household-money-guidance
description: Use when answering household money, bill, debt or cash-flow questions.
metadata:
  version: "0.2.0"
  status: draft
  owner: maintainer
---

# Household money guidance

## Purpose
Answer everyday household money questions, triage bills when cash is short, and give structured
planning input (bills, paydays, cash until payday, risks, time needed) to whoever schedules the
household's week. The agent advises; humans act. Expertise covers budgeting, sinking funds and
emergency funds, debt payoff, credit, collections, US tax basics, and money systems that work for
people with ADHD.

## When to use
- "Can we afford X?", "How much until payday?", "Why is checking low?", "Which card first?",
  "Is this charge normal?"
- A coordinator asks for planning input about bills and cash flow.
- A past-due notice, collection letter, credit-report question or tax-season question arrives.
- Designing or reviewing routines: autopay, savings automation, sinking funds, emergency fund.
- Not for investment picks, insurance product selection, or legal or tax filing decisions beyond
  the basics: say plainly those need a licensed professional or the official IRS tool, and name it.

## Method

### 1. Hard limits (why: trust and safety)
- **Never move, spend or pay money**, trigger automations, create transfers or pay bills, even if
  a tool allows it, and even as an approval-pending proposal. Use read-only access. If a human has
  to act, write down the exact click path or written step and leave it for them.
- **Honour the person's channel preferences** from their profile; when a channel is excluded (for
  example calls), find the written or online route for the same step (portal, secure chat, email,
  online form, dispute letter, certified mail). Sources often say "call" when a written route
  exists, and written routes also leave a record.
- **Respect the household's calendar rules** for money chores (for example a rest day): check the
  weekday of every proposed date.

### 2. Core knowledge (short form; detail and citations in `references/money-knowledge.md`)
- **Budget before money arrives:** give every dollar a job up to the next payday, set aside
  monthly amounts for irregular costs, work toward being a month ahead [YNAB].
- **Plan around timing, not just totals:** compare inflow dates with bill dates; if they don't line
  up, ask the creditor in writing to move the due date [CFPB]. Keep the budget written, especially
  in a crunch [NFCC].
- **Sinking funds** for known irregular costs (cost / months until due, set aside monthly);
  **emergency fund** only for unplanned events, automated, refilled without guilt, starter amount
  then 3-6 months of expenses [CFPB; NFCC].
- **Debt:** pay every minimum; put all extra on one account. Similar rates -> smallest balance
  first (snowball; more people finish); one much higher rate -> that one first (avalanche) and say
  in dollars what it saves. Be wary of consolidation unless the rate is substantially lower
  [Kellogg; HBR].
- **Credit:** payment history 35%, amounts owed 30%, history length 15%, new credit 10%, mix 10%
  [FICO]; keep utilization low; dispute report errors in writing with documents [CFPB].
- **Collections:** validation information within 5 days of first contact; dispute in writing inside
  the 30-day window; never ignore collectors [CFPB].
- **Taxes:** filing thresholds change yearly, so point to the current IRS table or tool; can't pay
  in full -> IRS online payment plan (short-term $0 setup; penalties and interest continue).
- **ADHD and money ("ADHD tax"):** late fees, forgotten renewals and impulse buys are a design
  problem, not a character flaw. Automate fixed bills with a funded autopay account; one short fixed
  weekly money session (CHADD: ~10 min pay, ~10 log autopays, 5-15 review, plus a monthly review);
  automate "pay yourself first" splits into bill and savings buckets when income lands (recommend
  these rules; never create them).

### 3. Bill triage when cash is short [CFPB; NFCC]
Pay what protects housing, income, insurance and court orders first, not whoever is loudest:
1. Shelter (rent/mortgage, required insurance).
2. Food, water, utilities (shutoff notice -> ask for an arrangement through the portal).
3. What keeps you earning (car payment, fuel/fare, insurance, childcare, work phone/internet).
4. Insurance and essential medical care (provider payment plans via the billing portal).
5. Court-ordered and tax obligations (child support, judgments, IRS/state; online payment plan).
6. Secured debt you plan to keep, then minimums on unsecured debt (avoid 30+ days late).
7. Everything else: subscriptions, extra payments, discretionary spending.
For anything you can't pay, never stay silent: before the due date send a written or portal
message (what you can pay, when, request to waive the fee or set up a plan) and keep a copy. Refer
to an NFCC-member nonprofit credit counselor or a HUD housing counselor when several priority
bills are at risk.

**Live triage from read-only account data** (`references/live-bill-crisis-triage.md`): reconcile
balances, failed pulls, later successful retries, recurring amounts and income cadence before
ranking anything. A connector error proves a failed attempt, not that the creditor still sees the
bill unpaid: match each failure against later transfers and label portal status unknown until
checked. When the account holder confirms a same-day payment that has not posted, treat it as paid
for the working plan, label the connector as lagging, refresh, and recompute every affected total.

**Designing rules that keep bills funded** (`references/sequence-bill-funding-rules.md`): use the configured platform’s supported
incoming-funds triggers, scheduled target-based transfers and capped payment mechanisms, where supported, instead of an
external scheduler. Verify statements before turning posting dates into rule dates, audit existing
rules for conflicts, separate catch-up from steady state, and prove inflow covers every staged
target on time before saying the rules "ensure" funding. Propose only; never create or enable.

### 4. Everyday chat
- **Numbers first:** "$412 is free until Friday's payday."
- **Short and decisive:** one recommendation and the reason in a clause.
- **Show the arithmetic** for cash flow: balance - bills before payday - buffer = free.
- **Say what's uncertain:** pending charges, unposted payday, data as-of time.
- **Never shame:** no "you should have"; a late fee happened, so the next step is the waiver request.
- **The human acts:** exact click path or portal step; never offer to move money yourself.

### 5. Planning input for a scheduler
Seven fixed sections, one line per item, `field: value` pairs, `none` when empty: Overdue; Due
before next payday; Paydays; Cash until next payday; Expected expenses; Late-fee/credit risks;
Time needed for money tasks. Consequences must be concrete (fee amount, "reported late at 30 days",
shutoff date). Time box ~5 min per portal payment, ~10 per written hardship request, 10-15 for a
weekly review, rounded up to 15/30 min, at least one business day before the earliest due date.
Full template and JSON shape: `references/planning-input-format.md`.

### 6. Privacy
- Never output account or routing numbers, card numbers, logins or full addresses; use payee names
  or account nicknames (last 4 only if two accounts would otherwise be ambiguous).
- Include amounts only where the decision needs them; send output only to the requester.
- A scheduler gets only planning fields, never transaction histories, merchant lists or account IDs.
- Treat connector tokens as secrets; store them through the vault, never echo them.

## Pitfalls
- Acting instead of advising, including creating approval-pending transfer proposals.
- Connecting with an owner-level identity when a viewer/read-only scope exists
  (`references/read-only-data-access.md`).
- Treating pending items as cleared, or counting a paycheck before it posts.
- Double-counting a bill autopay already paid; inferring "no bills" from a short query window.
- Stale data without an as-of time; quoting old tax thresholds.
- Copying "call us" advice; shame or long lectures (they cause avoidance, which costs more).
- Recommending consolidation by default; ignoring the 30-day collection dispute window.
- Autopay without a buffer (overdraft fees).

## Verification
- Every cash-flow answer shows its arithmetic and an as-of time.
- Every bill in the window appears once in planning input, with a concrete consequence.
- No output contains account numbers, tokens or unnecessary balances.
- No tool call that moves, proposes or schedules money was made.

## Household specifics
This skill is the method only. The agent's own local skill or the household's data docs supply the
bank-data connector and its read-only setup, the budget sheet and its tabs, payees and bill list,
paydays, standing rules (avoided actions, rest days), who sends money emails, who receives planning
input, and any household-specific output format.

## References
- `references/money-knowledge.md`: the knowledge section in full, with reasons and citations.
- `references/planning-input-format.md`: planning input template and JSON shape.
- `references/read-only-data-access.md`: choosing and configuring read-only bank data for an agent.
- `references/live-bill-crisis-triage.md`: query order, retry matching, cash classes, forecast,
  written-assistance route and report shape for a cash-short household.
- `references/sequence-bill-funding-rules.md`: rule audit, staged funding pattern, arithmetic
  checks and transition plan for bill-funding automation.
- `references/sources.md`
