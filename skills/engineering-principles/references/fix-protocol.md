# Fix protocol

How a fixing agent responds to review findings, bug reports and lint failures. The core idea: a
finding on one line is a sample from a class. If you fix only the sample, the rest of the class
stays in the code, and the same comment comes back next week, or the same bug returns through a
path nobody patched. So before you change anything, you work out what the class is, where else it
lives, and where its members share a cause. Then you fix it there, once, and add something that
stops it coming back.

This is the same discipline security researchers call variant analysis, applied to everyday code
quality.

## Contents

0. Separate the outcome from the prescribed mechanism
1. Read and verify the findings
2. Group findings into classes
3. State the root cause
4. Calibrate the search
5. Widen the search step by step
6. Judge every hit
7. Choose the fix level
7a. Preserve capabilities through a rewrite
8. Guard the class
9. Verify
10. Report
11. Out-of-scope instances
12. Anti-patterns
13. Credits

## 0. Separate the outcome from the prescribed mechanism

Feedback often arrives with a fix attached. Split it into four parts before designing anything:

- **Required outcome:** what the person must be able to see or do afterwards.
- **Evidence:** errors, screenshots, measurements, examples.
- **Hypothesis:** someone's explanation of the cause.
- **Suggested implementation:** the flag, prop, branch, cache or rewrite they asked for.

Honour the outcome, verify the evidence, test the hypothesis, and adopt the suggested mechanism only
if it fits the existing design. If it conflicts, say why and build the closest compliant design that
reaches the same outcome. Never turn an unverified explanation into the design.

## 1. Read and verify the findings

Read every finding in the round before changing any code. Findings are often related, and fixing
the first one in isolation can make the third harder or wrong.

For each finding, check it against the code. Reviewers are usually right but not always: the line
may have moved, the "duplicate" may encode different knowledge (EP-02), the cited rule may not
apply. If a finding is wrong, say so with evidence (a test, a caller search, the rule's actual
scope). If it is unclear, ask on the PR (or your task tracker) before implementing anything. Agreement without
verification wastes a round; so does performative thanks.

## 2. Group findings into classes

Sort the findings by the principle or project rule they cite, then by underlying cause. Three
comments about inline plan checks, a missing trial case, and a duplicated pricing condition may be
one class. Work class by class, not comment by comment.

A typo is a typo; not every finding is a class. If you cannot name a principle a finding violates,
treat it as a single edit (or a question back to the reviewer).

## 3. State the root cause

Write one sentence that names the principle, the construct, and the missing property:

> This code violates `<EP-nn or project rule>` because `<construct>` appears where `<required
> property>` is missing.

*Example:* "This code violates EP-01 because plan-gating conditions are written inline at call
sites where one shared, tested predicate is missing."

If you cannot write the sentence, you don't understand the finding yet. Ask "why did this happen?"
until you reach something that, once fixed, prevents the whole family (EP-09). Five times is a
prompt, not a quota. The sentence is also your search specification: it says what to look for.

## 4. Calibrate the search

Write a search that matches the reported instance exactly. If it doesn't, your root-cause sentence
is wrong or too narrow; fix the sentence before going further. This step is cheap and catches the
most common failure, which is confidently searching for the wrong thing.

Use the plainest tool that works: `rg` for text, structural search (ast-grep, Semgrep, CodeQL)
when shape matters, the project's semantic doc search for rules and rationale.

## 5. Widen the search step by step

Generalise one element of the search at a time and run it again, recording each query and its hit
count. Useful directions to widen along:

- **Names:** related identifiers and synonyms that exist in this codebase (check they exist before
  assuming they do).
- **Siblings:** files created alongside the original or copied from it; copy-paste bugs cluster
  there.
- **Call sites:** every caller of the API involved; misuse of an API clusters wherever it is used.
- **Shapes:** the same mistake written differently (`a || b` vs `[a, b].includes(x)`, a loop vs a
  reduce).
- **Languages and layers:** the same knowledge encoded in another service, language, config or doc.
- **The majority pattern:** where most code does X and a few sites deviate, the deviants are
  candidates.

Stop widening when most new hits are noise. If more than about half of a step's new hits are false,
step back to the previous query.

Scope: search the area the class can plausibly reach (the package, the app, the repo). If the class
clearly reaches beyond what this change should touch, keep searching to learn its size, then see
section 11.

## 6. Judge every hit

For each hit, decide true or false and write down why. Argue against it before accepting it: is
there a guard elsewhere, is it intentional and documented, does it encode different knowledge, is
it test data? A hit survives only if you looked for those reasons and didn't find them. An
unreachable but unprotected instance is still an instance; note it at lower priority rather than
discarding it.

Keep the ruled-out hits and their reasons. They are part of the evidence, and they stop the next
reviewer from re-raising them.

## 7. Choose the fix level

Pick the smallest change that removes every true instance, placed at the cause they share (EP-11):

- Usually that is one shared unit fixed or created once and used everywhere: a predicate, a
  constant, a component or variant, a type, a parser at the boundary.
- If the shared unit already exists but some sites bypass it, the fix is to route them through it.
- If a shared unit has decayed into flags and modes, the fix may be to inline it and re-extract
  the real concept (EP-02).

Avoid both failure modes. A one-off patch per site leaves the cause and invites drift. A new
framework for a two-instance class is overengineering (EP-06). Add only the abstraction the true
instances need.

Before changing a shared unit, search its callers for anyone depending on the behaviour you are
changing (EP-09). When the fix is a refactor followed by a behaviour change, make them separate
commits: first the behaviour-preserving extraction, then the change in one place.

## 7a. Preserve capabilities through a rewrite

Before replacing a unit, list what it does today: observable behaviour, public API and data shape,
error semantics, supported modes and platforms, accessibility, operational and authoring workflows.
Mark each item preserve, adapt without loss, correct a demonstrated defect, or replace with explicit
approval. A capability is not removed just because its old implementation broke a rule; design a
compliant replacement. Scale the record to the risk: a line for a local fix, a PR section for a shared
or compatibility-sensitive change.

## 8. Guard the class

Add something that would have caught every instance and will catch the next one:

- a test that fails before the fix and passes after (write it first, watch it fail);
- a lint rule or structural search in CI, with a message that names the principle and the fix;
- a type that makes the wrong state unrepresentable (EP-07).

If a new lint rule would fire on an existing backlog, ship it as a warning, fix the backlog, then
make it an error. If no guard is feasible, say why in the report.

## 9. Verify

Before you call the class fixed, check that the code you touched still fits the system as it runs
today, not only the pattern you searched for. Use what the repo records (decision records, docs,
configuration, recent history); a missing live credential does not excuse the check. Names, settings, endpoints and auth that describe a
retired setup are a class of their own (see the review protocol, section 2); fix them in the same
change when they are in scope, or mark them per section 11.

Rerun the project's checks and the guard, and paste the exact commands and results. Rerun your
widened search to show the true instances are gone. A fix is done when the evidence says so, not
when the edit is saved.

## 10. Report

Reply per finding on the PR thread (and in your task tracker, if your harness has one), using the
reviewer's finding ID:

```text
R1-03 fixed as class (EP-01).
class: Plan-gating conditions written inline instead of one shared, tested predicate.
root cause: No shared predicate existed, so each page re-derived the rule; two copies missed the trial period.
queries: rg -n "plan ===? '(pro|team)'" web api (9 hits) -> rg -n "trialEndsAt|isTrial" web api (14 hits)
fixed: web/src/checkout/CheckoutPage.tsx:42, web/src/billing/Upgrade.tsx:17, api/billing/gate.py:88, web/src/nav/Badge.tsx:9 (found by widening)
ruled out: 6 hits in tests/fixtures (test data); web/src/admin/Impersonate.tsx:30 (checks staff role, not plan)
guard: hasPaidFeatures tests cover every plan x trial edge; lint rule no-inline-plan-check (warn; backlog now 0, switched to error)
verification: pnpm test billing (48 passed), pnpm lint (0 errors)
```

"No other instances" is a valid outcome only with the queries that showed it.

## 11. Out-of-scope instances

Sometimes the search shows the class reaching well beyond this change: another package, a risky
shared primitive, a different service. Fixing all of it here would turn a small fix into a
rewrite; fixing none of it and saying nothing hides a known defect.

Fix the instances within the change's scope that are safe to fix, and fix or create the shared
cause if that is in scope. For each remaining instance, leave a structured marker at the site:

`@todo(EP-nn): <class in one sentence> — <what the fix should be>`

and list the markers you added, with the queries that found them, in the PR description. The
marker is the hand-off: whatever tool or platform runs the project can collect markers on a
schedule and turn them into tickets (decision D-003). A marker is a precise description of owed
work, not a place to hide it, so write it as if the next reader has never seen this PR. Do not
leave known instances unmarked.

## 12. Anti-patterns

- **Line-only fix:** changing exactly the quoted line while the calibrated search still has hits.
- **Patch per site:** the same local edit repeated N times instead of fixing the shared cause.
- **Rewrite for a nit:** restructuring a module to address one naming comment.
- **Hard-coded special case:** making the failing input pass (`if id == 'x'`) instead of fixing why it
  fails.
- **Hiding the symptom:** swallowing an error, adding a retry or a null-coalesce without asking why.
- **Searching without calibrating:** a widened query that never matched the reported instance.
- **Silent ruling out:** dropping hits without a recorded reason.
- **No guard:** a class fix that nothing stops from regressing.
- **Agreeing without checking:** implementing a finding you haven't verified against the code.
- **Quiet deferral:** leaving out-of-scope instances unreported, or reported only in a code comment.
- **Caller-shaped special case:** a flag, variant or endpoint named after one screen, ticket or caller,
  or a boolean that exists only to bypass shared behaviour for one consumer.
- **Parallel machinery:** a second registry, parser, cache or retry loop beside an existing one.
- **Green but gutted:** keeping a screenshot or test green while discarding the workflow that produced
  the feature.

## 13. Credits

The search-calibrate-widen-triage method follows variant analysis as practised by GitHub Security
Lab (CodeQL) and described in Trail of Bits' variant-analysis skill; it is re-expressed here in our
own words, and no text from that CC BY-SA work is reproduced. The "verify before implementing, ask
before assuming, no performative agreement" stance and failing-test-first are adapted from
obra/superpowers (MIT). The case for fixing classes draws on Google Project Zero's 2022 finding that
many in-the-wild exploits were variants of incompletely patched bugs, and on the Beyoncé Rule from
*Software Engineering at Google* ("if you liked it, you should have put a CI test on it").
