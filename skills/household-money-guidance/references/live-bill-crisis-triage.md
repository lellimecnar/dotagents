# Live Bill-Crisis Triage From Read-Only Account Data

## When to Use

Use this procedure when the household says bills are overdue, cash is short, automatic payments have failed, or the next month looks unaffordable. It finds what is actually unresolved without mistaking every connector error for a delinquent bill.

## 1. Freeze and enforce scope

1. Obtain written agreement on the included people/entities, account classes, data window, report contents, and actions that remain forbidden.
2. Build explicit included and excluded account sets from the live account list. Exclude business, trust, child, and investment accounts unless the agreed scope names them; an account being visible does not put it in scope.
3. Record an exact cutoff timestamp and household timezone. Use the same cutoff and window on every ledger query.

## 2. Establish cash that is actually known

1. Read the current scoped balance snapshot, if available; check its scope and timestamp.
2. Follow the configured connector’s documented pagination to enumerate all in-scope accounts.
3. Fill gaps from authorized per-account balance data; do not query or count the same economic balance twice.
4. Classify every result before totaling:
   - funded reserve, income account or deposit balance: asset;
   - credit card or loan: liability, never cash;
   - investment: outside bill-paying cash unless explicitly included;
   - a liability and its funding reserve have distinct economic roles; do not deduplicate them as if they were the same record.
5. Use available rather than ledger balance when holds exist. Calculate in the relevant currency’s integer minor units.
6. Report three cash figures separately when present:
   - known available cash;
   - cash earmarked for bills;
   - balances unavailable because of login, provider, permission, or stale-data errors.

Never present a complete household cash total while an included depository account is unavailable. Name the missing account, not its internal ID, and make reconnection the first physical action when it blocks the decision.

## 3. Reconcile overdue candidates from transfer history

1. Read authorized payment history for every in-scope account across the agreed window; confirm that records reflect real activity. Follow documented pagination until every page is covered.
2. Use authorized statements or transaction data as supplemental evidence; do not substitute them for missing in-scope payment history.
3. Extract external bill pulls and payments. Ignore internal routing when identifying whether a creditor was paid.
4. Verify each candidate using the relevant funding account’s outgoing-payment history. This compact history is the verification pass after the broad ledger query.
5. Normalize each obligation by source account, payee/description, amount, and billing cycle. Treat an initial failure and its near-date retry as one obligation, not two bills.
6. Mark an obligation **resolved** when a later completed transfer matches the failed attempt or retry. Do not list it as overdue or recommend paying it again.
7. When the account holder reports that a payment was made after the ledger cutoff or through another account, treat that firsthand confirmation as the current working status even if the connector has not posted it. Refresh same-day managed transfers, external transactions, and the funding balance; then label the payment “confirmed paid by account holder; connector not yet posted” rather than leaving it in the overdue total. This prevents sync lag from overriding newer evidence.
8. Mark an obligation **unresolved failed pull** only when neither a later matching completion nor a current account-holder confirmation exists through the cutoff. This is still not proof of creditor delinquency: it could have been paid outside the connector. Say “potentially overdue” or “unresolved in the connector” until the creditor portal confirms status.
9. Preserve ambiguous destination labels literally and describe them as labels. Never guess which creditor or account a mixed or truncated payee name represents.

## 4. Estimate upcoming bills conservatively

1. Infer recurrence only when the history shows a repeatable payee and cadence.
2. Use the most recent completed or attempted amount for the next-cycle estimate; show a range or “recent amount” when it varies materially.
3. Treat connector timestamps as posting dates, not contractual due dates. State “around” or “historically posted near” unless a current statement or account field supports an exact due date.
4. Ignore stale due-date, minimum, statement, or balance fields as current evidence. Include their as-of date when they remain useful historical context.
5. Use summaries only as cross-checks. Do not substitute incomplete or low-confidence summaries for scoped records. A profile is a cross-check, not the authoritative live bill ledger.

## 5. Project income without manufacturing certainty

1. Review incoming deposits for each in-scope source using the connector’s documented transaction filters.
2. Group deposits by source description before inferring cadence; one income account can receive weekly and monthly payroll streams.
3. Derive future dates from observed intervals and compute variable-pay estimates with the median in integer cents. Do not use one unusually high or low deposit as the norm.
4. Project only through the agreed horizon and label every future deposit as expected, not guaranteed.
5. Compare expected inflows with unresolved and upcoming obligations, but never recommend committing every projected dollar. Reserve housing-adjacent essentials, food, utilities, insurance, transportation, and other known living costs first.

## 6. Calculate the triage gap

1. Map each unresolved bill to its funding reserve or account.
2. Show `unresolved amount - matching available balance = preliminary gap` for decision support.
3. If several creditors share a funding account, count its balance once and identify the competing claims.
4. Keep inaccessible depository balances outside the gap calculation and state that the gap is preliminary.
5. Keep already completed payments in a short “do not pay twice” list.

## 7. Verify accessible assistance routes

For every at-risk priority bill, check the creditor’s official current site for a portal message, secure chat, direct message, online hardship application, email, or mailed form. Offer a verified route the requester can safely use. If no suitable channel is available, state what the creditor actually offers.

A written request should ask for:
- the exact amount required to become current;
- fees already assessed and whether credit reporting has occurred;
- a fee waiver, due-date change, repayment plan, short deferral, or forbearance option;
- processing time and written confirmation.

Do not promise a payment amount or date until known available cash and essential expenses support it.

## 8. Report in a calm, startable shape

Lead with one sentence that distinguishes a timing problem from a total-income problem only when the data supports that distinction. Then use this order:

1. as-of timestamp and known-cash limitation;
2. total unresolved failed pulls and a prioritized table: obligation, amount, last failed date, matching available balance, status confidence;
3. payments already completed, to prevent duplicates;
4. near-term expected deposits and upcoming recurring bills, clearly labeled as estimates;
5. one first physical action, normally reconnecting the most important missing depository account or opening the highest-priority creditor portal;
6. housing, essential utilities, income-preserving secured debt, insurance, and court/tax obligations before unsecured minimums and nonessential items;
7. verified, accessible support routes for obligations that cannot be paid safely.

Exclude raw transaction histories, account IDs, full account numbers, and irrelevant liability totals. End by stating that no money moved and no rule or autopay changed.

## Pitfalls

- Reconcile retries before totaling failures — the initial attempt and retry are one obligation, and a successful retry removes it from the overdue list.
- Finish pagination before declaring totals — the agreed interval may span multiple result pages.
- Prefer targeted account-transfer histories for the final verification pass — a broad multi-account ledger is complete but hard to audit bill by bill.
- Separate known cash from inaccessible cash — treating login-required balances as zero fabricates a shortfall.
- Use available rather than ledger balance when holds exist — held money cannot safely fund a bill.
- Keep forecast income and observed cash in separate columns — expected deposits are not current funds.
- Do not let a low-confidence or narrow financial profile override the scoped live ledger — aggregation can omit the very bills under triage.
