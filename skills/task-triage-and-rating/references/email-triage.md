# Email triage rules (read-only)

Record a task only if ALL of these hold:
- Someone (or a system) needs or expects a specific act by the user, AND
- the act isn't already done (check the thread for a later 'done' or 'confirmed'), AND
- skipping it has a consequence, or the user clearly cares (their own request, family
  logistics, appointments, bills due, forms, RSVPs, renewals, deliveries needing action).

Actionable examples: 'please sign and return by Nov 20', 'your renewal is due', a school
form, an RSVP request, an appointment to confirm online, a reply they owe, a package to pick
up, a bill (record 'review & pay bill - user decides'; never pay it).

Not tasks (skip): newsletters, marketing, promotions, sale alerts, receipts and order
confirmations (unless they include a return deadline or required action), shipping updates
with nothing to do, FYI/cc-only messages, automated notifications already resolved, social
digests. Judge spam or phishing only on evidence (the sender's registered domain, SPF/DKIM
results, sender history, where links really point) and record that evidence; never click links.
Never call a mail phishing without that evidence, and create no task or money hand-off from a
sender you could not verify: note it as `unverified` with what you checked. An
FYI with a date that matters (an event) becomes a calendar note for the coordinator, not a task.

Recording
- Title: a verb-first next action. Labels and fields as in section 3; description:
  `src:email|<sender name <address>>|<date received>|"<subject>"`, a one-line quote of the
  request, and the sentence the due date came from. Include the mail message/thread id if available.
- If the email says 'call us', look for a web form, portal or email address and record that route on the task.
- If the email needs a reply, the task is 'Draft reply to X re Y' (the user sends it).

Duplicate detection (before creating a task)
- Same thread id: update the existing task (new due date, new info). Never create a second one.
- Same sender domain + same object (account, order or form name) + overlapping dates: treat
  it as a duplicate. Merge, keep the earliest hard deadline, and append the new src line.
- Reminder emails ('2nd notice'): don't create a duplicate. Raise postponed/priority if overdue.
- Title roughly 80%+ similar to an open task: merge without asking, and mention 'merged' in the reply.

## Money emails -> the household's finance agent

If a finance agent has no mail access, the task manager is its only source of bill and money mail.
Money emails: bills, statements, invoices, "payment due" notices; autopay scheduled / processed /
FAILED, bill payment confirmations; late notices, past-due, collections, overdraft or low-balance
alerts; price, premium or rate changes on recurring bills, new subscriptions, renewals, trials
ending; paychecks, refunds, reimbursements or other dated incoming money; tax documents and
tax-agency letters. Not money emails: receipts for one-off purchases already paid, marketing,
sale alerts, bank newsletters.

For each money email create one hand-off card for the finance agent:
- title: `Money email: <payee> - <kind>` (kind: bill | autopay | autopay-failed | late | income |
  price-change | subscription | tax | other)
- idempotency key: the mail message id, so re-reading the same email never makes a second card
- body, these lines only, `unknown` where the email does not say:
  ```
  payee: <name as on the email>
  kind: <kind>
  amount: <dollars.cents | unknown>
  due_date: <YYYY-MM-DD | unknown>
  autopay: <yes | no | unknown>
  pay_route: <portal URL domain | mail | unknown>
  received: <YYYY-MM-DD>
  mail_id: <message id>
  note: <one line: what changed or what is asked>
  ```
Never put account numbers (even the last 4), login links with tokens, other balances, or the email
body on the card: shared boards are visible to other agents. Still record the person's own task
("review & pay bill - person decides") when they must act; the card does not replace it, and the
task manager never pays or schedules a payment. Do not wait for the finance agent; continue triage.
One card per email, even for several emails from the same payee.
