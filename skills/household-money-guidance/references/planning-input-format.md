# Planning input format (reply to a scheduler or coordinator)

Always output these eight sections, in this order. Write one line per item using
`field: value` pairs separated by ` | `. Write `none` for an empty section. No prose,
headers only. Dates are ISO (YYYY-MM-DD). Amounts are rounded to whole dollars unless
cents matter.

```
## Overdue
payee: <name> | amount: $<n> | due: <date> | days_late: <n> | method: <portal/autopay/mail> | autopay: y/n | consequence: <fee $/credit report at 30d/shutoff/none>
## Due before next payday
payee: <name> | amount: $<n> | due: <date> | method: <portal> | autopay: y/n | consequence: <...>
## Paydays
source: <employer/benefit> | amount: $<n from the pay record or posted deposit> | date: <date from the pay schedule or posting> | checked: <source>
## Cash until next payday
available: $<n> | committed: $<n> | free: $<n> | as_of: <timestamp> | next_payday: <date>
## Expected expenses
item: <groceries/sinking-fund item> | amount: $<n> | by: <date> | funded: y/n
## Late-fee/credit risks
payee: <name> | risk: <fee $n / 30-day credit mark / shutoff> | deadline: <date> | fix: <autopay on / pay by date / written hardship request>
## Time needed for money tasks
task: <pay 3 bills via portals> | minutes: <n> | before: <date> | not_on: <household rest day, if any>
## Decisions
question: <what only the account holder knows or decides> | checked: <sources checked> | recommendation: <your answer>
```

Rules:
- Include every bill and payday in the window. Leave out explanations, history, and
  anything else not needed to schedule.
- `consequence` and `risk` must be concrete: the fee amount, "reported late at 30 days",
  a shutoff date, or `none`.
- Time box: plan about 5 minutes per portal payment, 10 for a written hardship or
  fee-waiver request, and 10–15 for a weekly review, based on CHADD's weekly schedule.
  Round the block up to 15 or 30 minutes. [16] Pick a date at least one business day
  before the earliest due date, and never on a day the household's rules exclude.
- Never estimate or assume. Every amount, date and status comes from a source (statement,
  creditor portal, posted transaction, bill notice, pay record). If data is missing or stale, get
  it from that source or from the agent that holds it first. A value no source confirms is left out.
- If only a bill's amount is unconfirmed, keep the line without an amount and write the payment as
  "pay the amount the portal shows".
- A fact only the account holder holds (for example whether they paid outside the connected
  accounts) goes under Decisions, naming what was checked.
- Pay that has not posted is never counted as cash.
- Never match a bill to a payment of a different amount.
- If the requester reads JSON, put the same data in the task-completion metadata, with keys `overdue`,
  `due_before_payday`, `paydays`, `cash_until_payday`, `expected_expenses`, `risks`,
  `time_needed`, `decisions`. Each key holds an array of objects using the same field names; use an
  empty array for none (`cash_until_payday` is a single object). Add `as_of`
  (ISO timestamp) and `source: "<data connector>"` at the top level.

Example (illustrative only):
```
## Overdue
none
## Due before next payday
payee: Electric Co | amount: $142 | due: 2026-11-20 | method: utility portal | autopay: n | consequence: $15 late fee
```
