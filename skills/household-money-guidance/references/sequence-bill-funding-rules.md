
# Bill-funding rule design

## When to Use

Use this read-only procedure with the configured funding platform. Treat Sequence-specific terminology and documentation below as an optional worked example. Do not create or change rules.

## 1. Establish contractual inputs

1. For each in-scope payment, record payee, funding account, contractual date, expected amount, payment initiator and whether the amount varies.
2. Read current statements or creditor portals for due dates and amounts. Treat transaction timestamps only as historical posting dates; use them to propose tentative staging dates, never to claim a contractual due date.
3. For variable liabilities, prefer an appropriately capped payment based on current statement data if supported by the configured platform. Otherwise use a conservative fixed reserve and state how it must be reviewed.
4. Round reserve targets upward deliberately and show the buffer. Never hide a guessed amount inside a precise-looking target.

## 2. Audit the existing automation before designing

1. Enumerate all rules affecting in-scope income, reserves and liabilities using the configured platform’s documented list and detail operations.
2. Review recent rule runs for failures, partial allocations, pending approvals and completed transfers. A rule description is not proof that its branches actually fund the intended pod.
3. Map trigger, conditions, ordered actions, source, destination, cap, and schedule. Flag these conflicts before proposing replacements:
   - a transfer-amount threshold that excludes normal paychecks;
   - a target below the current bill;
   - a fallback branch that omits a priority bill;
   - two rules funding the same destination from different shared pods;
   - a scheduled rule that cannibalizes a pod needed by another creditor;
   - a liability-payment rule whose source reserve is smaller than its cap.
4. Preserve routing rules outside the agreed scope.

## 3. Choose the built-in pattern

Prefer the smallest supported design that separates bill money from spending money:

1. **Income intake:** identify where income arrives.
2. **Bill reserve:** propose a dedicated reserve for bill funds if useful and supported.
3. **Incoming split:** propose using a supported trigger to allocate net income between bill reserves and spending. Derive the percentage from verified monthly bill targets divided by conservative monthly net income, then add a visible buffer.
4. **Staged funding:** propose transfers ahead of verified due dates and processing windows. Fill each shortfall using a supported target-based mechanism.
5. **Payment:** preserve the authorized payment method and verify that the account funding it has enough cash before processing.

Use a shared bill pod only when its staged cumulative targets are explicit. For example, if one card pod funds three creditors, top it up to the first creditor's reserve before the first due date, then to the cumulative two-creditor reserve before the second. This preserves existing ACH setup without pretending each creditor has the entire shared balance.

Dedicated per-creditor pods are safer when amounts vary independently or one creditor can drain another's reserve, but they require changing the creditor's funding account. Do not recommend that migration unless its maintenance cost is justified.

## 4. Order actions by consequence and timing

1. Housing first, then essential utilities, income-preserving secured debt, insurance or court/tax obligations, unsecured minimums, and discretionary services.
2. Within a priority class, order by the verified due date and payment processing lead time.
3. Verify how the configured platform orders competing actions and handles insufficient funds. Give higher-priority bills precedence where the rules support it.
4. Where supported, prefer a deficit-only transfer when balances should roll forward. Verify shortfall behavior before proposing the rule.
5. Use limits on variable liability payments so one unexpectedly high balance cannot drain reserves for later bills.

## 5. Separate catch-up from steady state

A stable rule set cannot instantly repair an overdue cycle. Build two explicit phases:

1. **Catch-up phase:** route a temporarily larger share of incoming money to the highest-priority overdue obligation or its funding reserve until the current cycle is funded. Give the exact disable/switch point.
2. **Steady state:** enable the sustainable incoming split and staged monthly top-ups only after the current cycle is caught up.
3. Never layer the replacement on top of the old income-distribution rule. State which old rule must be paused and which payment rules remain.
4. Check whether a trigger change requires replacing the rule, and document a safe transition for the configured platform.

## 6. Prove the design with deterministic arithmetic

Keep cents as integers and calculate:

- maximum monthly bill target;
- conservative monthly net income by source;
- `bill target / net income` funding ratio;
- proposed share allocated to bill reserves and the monthly buffer;
- cash available before each staged funding date;
- cumulative target for every shared pod;
- remaining spending allocation after bill funding.

Use median variable income and observed cadence, not the highest paycheck. Include only expected deposits that arrive before each scheduled rule. If any stage is short, move the rule later only when the creditor's processing window allows it; otherwise reduce lower-priority payments, obtain a written arrangement, or say the design cannot guarantee every due date.

Do not say automation “ensures” funding merely because the monthly total works. Timing must work at every scheduled stage, and food, fuel, insurance, and other essential outflows must remain viable.

## 7. Transition and verification checklist

Before the human enables anything:

- contractual amounts and due dates verified;
- new rules created but not yet competing with old rules;
- old conflicting rules identified by name;
- scheduled dates respect weekends, processing lead time, and household policy;
- variable payments capped;
- one complete cycle simulated or manually calculated;
- current overdue cycle handled separately;
- plan to verify first execution in the configured platform;
- name the authorized person who will check variable bills and failures, and agree on a cadence.

Report the proposal in this order: rules to keep, rules to pause, new account/pod, incoming rule, scheduled rules in execution order, monthly arithmetic, catch-up transition, statement facts still needing confirmation, and the read-only/no-change statement.

## Pitfalls

- Verify resulting allocations even when the automation reports success; a successful fallback may omit a required bill.
- Do not use percentages alone for fixed due-date obligations — variable income can leave a fixed bill short even when the annual average works.
- Do not top up a shared source pod from another shared bill pod — the later rule can steal money reserved for a different creditor.
- Do not schedule all monthly funding on one date unless the reserve is already one full cycle ahead — a catch-up household may need staged funding around actual income dates.
- Do not derive exact due dates from ACH posting history — weekends, retries, and lender processing shift posting dates.
- Do not hide the setup transition — duplicate rules can move the same income twice or drain the wrong source before the replacement is ready.

## Optional worked example: Sequence documentation

- Rules overview: https://support.getsequence.io/hc/en-us/articles/34371183108243-Rules-Overview
- Triggers: https://support.getsequence.io/hc/en-us/articles/34371275427219
- Conditions and Else branches: https://support.getsequence.io/hc/en-us/articles/34371347538963
- Ordered actions: https://support.getsequence.io/hc/en-us/articles/34372536007315
- Action types, Top Up, liability payments, and limits: https://support.getsequence.io/hc/en-us/articles/34372744192275-Action-Types
