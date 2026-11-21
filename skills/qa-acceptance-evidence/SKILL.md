---
name: qa-acceptance-evidence
description: Use when QA-accepting a change. Oracle, risk-ranked tests, evidence verdicts.
metadata:
  version: "0.1.2"
  status: draft
  owner: maintainer
---

# QA acceptance evidence

Verify observable behaviour, not implementation plausibility. A green test where the user action
did not occur is a failure. This skill is the acceptance method for a QA agent: how to derive the
oracle, design and prioritise tests, explore, check accessibility, pick regressions, and report a
verdict per criterion. Browser mechanics for exploratory web QA are in the bundled `dogfood` skill;
the accessibility method is `web-accessibility-check`.

## When to use

Acceptance planning, behaviour verification, exploratory testing, accessibility checks and
regression selection on any app or tool. Not for approving code by inspection alone.

## Principles

- **The running product is the evidence.** Code, unit tests, mock calls and the author's screenshots
  are scope clues, not acceptance evidence. [TD1][BV1][BV2]
- **Read the mutation back.** Perform the action, observe the immediate result, navigate away or
  reload, and read the target again. A toast without a changed target is a failure.
- **Risk changes depth, never the pass condition.**
- **Blocked is not Pass.** When a prerequisite or failure injection is unavailable, say what
  evidence is missing; do not infer.
- **One unexplained intermittent failure is a failure.** Repetition characterises frequency; it does
  not erase the defect. Preserve first-failure artifacts before retrying.

## Review sequence

1. **Establish the oracle.** Resolve conflicting sources by authority and date (oracle hierarchy in
   [references/test-design.md](references/test-design.md)); write each acceptance criterion as one
   observable Given/When/Then. Separate prototype promises from production promises.
2. **Map scope.** Entry points, callers, persisted data, events, shortcuts, permissions, devices,
   feature toggles, error paths, prior regressions; data-loss, privacy and off-machine-data risks.
3. **Risk-rank** impact × likelihood (critical/high/medium/low). Destructive, irreversible, export,
   persistence and media paths first. [TD1][RG1]
4. **Model the test space**: equivalence partitions, ordered boundaries, a state-transition table,
   and a decision table where conditions interact. Exercise every identified partition, boundary,
   required transition and rule. [TD1][TD2]
5. **Golden path end to end** from a named seed/reset, with read-back after navigation/reload.
6. **Negative and recovery paths**: empty/minimum, invalid, maximum realistic, Unicode/RTL,
   permission denial, stale/conflict, interruption, retry, cancellation, refresh during mutation,
   duplicate activation, unavailable dependency.
7. **Explore** in a chartered, reviewable session around the highest risk, letting each observation
   choose the next test. Exploratory testing is simultaneous learning, design, execution and
   interpretation, not random clicking. [ET1][ET2][ET3]
8. **Accessibility** per `web-accessibility-check`. Automated scans assist but cannot determine
   accessibility.
9. **Regression**: confirm the fix, then affected behaviours, risk-directed safety cases and the
   prior-bug pack ([references/regression.md](references/regression.md)). [RG1][RG2]
10. **Report and route**: Pass/Fail/Blocked per criterion with exact steps, observed vs expected,
    environment, timestamps, screenshots/clips, console/network evidence, measured timings. One
    failure blocks acceptance unless the owner explicitly waives it.

## Checking a UI spec or story before build

Gaps found before build are cheap. For each UI story or spec, confirm each item; turn every miss into
one exact question for the owner, never a gap filled with your own preference.
- **States:** empty, loading, error, overflow and long text, disabled, every supported theme, reduced motion.
- **Terms:** each UI concept is used as the project glossary defines it; a retired or undefined term is a gap.
- **Visual reference:** name an approved, identifiable design artifact, prototype or running example. An unspecified old UI is not a reference.
- **Keyboard and screen reader:** keys, focus order, focus return, accessible names and live
  announcements are stated (`web-accessibility-check`).
- **Reuse:** names the existing component it extends, or justifies a new one; no app-level twin of a
  design-system component. If a component already does this job, the spec says whether its behaviour is kept.
- **Acceptance criteria:** each is observable in the target interface through a named scenario or flow.
- **Scope:** deferred items are named as owed, with an owner, not silently dropped.

## Test-design rules

- **Partitions:** at least one representative from every valid and invalid partition; repartition
  when observed behaviour differs. [TD1]
- **Boundaries:** each boundary and the closest value in the adjacent partition; both neighbours for
  high-risk or historically defective boundaries. [TD1]
- **State transitions:** every required valid transition and every business-critical invalid one;
  assert source state, action, destination, side effects and persistence. [TD1]
- **Decision tables:** enumerate combinations when role, lock, toggle, network or artifact state
  changes the outcome; collapse only demonstrably equivalent rules. [TD1]
- **Scenarios:** include the real user goal and dependent systems; don't stop at a component
  boundary when acceptance is end to end.
- **Locators and oracles:** role/name locators and visible/audible outcomes; never CSS classes,
  component state, internal arrays or private methods as the pass oracle. [BV1][BV2]
- **Asynchrony:** wait for the expected state, not a fixed sleep; after success, assert that no
  duplicate result or late reversal appears. [BV2]
- **Isolation:** reset deterministic state for each independent criterion; record seed and
  environment, and verify the visible state actually reset. [BV2]

## Verdicts and thresholds

- **Pass:** every must-pass criterion executed; expected state observed; mutation independently read
  back; no new console error; evidence attached.
- **Fail:** wrong/missing/duplicate state, silent no-op, data loss, misleading success, inaccessible
  required action, unhandled error, or criterion not met.
- **Blocked:** prerequisite unavailable or injection unsupported; state what is missing.
- **Performance:** use the feature's stated budget. If none exists, record median and worst timing
  with data size and mark the release threshold Blocked rather than inventing one.
- **Accessibility:** any applicable WCAG 2.2 A/AA failure blocks; AAA is advisory unless required.
- **Product gates beyond mechanics:** every visible control acts observably or gives a focusable,
  accessible reason it can't; empty states teach the next action; deep links provide a return path.
  A mock/prototype is not proof of real persistence, devices, filesystems or engines.

## Failure modes → correction

| Failure mode | Correction |
| --- | --- |
| Toast appears but target never changes | Reopen/reload and compare the target state |
| Unit test passes but click path is broken | Drive the real browser; capture the visible result |
| Happy path only | Add invalid, boundary, interruption, concurrency, recovery, persistence |
| Test list mirrors code branches | Derive from user-visible partitions, states, rules, risks |
| Huge unprioritised regression list | Map impact; run critical targeted and safety tests first [RG1] |
| Axe-only accessibility sign-off | Add manual keyboard, focus, zoom, contrast, screen reader, media |
| Retry until green | Keep first-failure artifacts; investigate nondeterminism |
| "Looks good" report | Exact reproduction, observed/expected, evidence per criterion |
| Animation accepted from start and end frames | Slow it, capture mid-flight frames and sample positions ([references/verifying-animations.md](references/verifying-animations.md)) |

## Verification

Each criterion has a verdict and evidence; every mutation was read back; Blocked items say what is
missing; regression selection covers every identified impact or gives an explicit N/A reason.

## Project specifics

The project's QA skill or `AGENTS.md` (or a QA knowledge dossier) supplies how to run and reset the
app, seeds, authority order of product docs, known regressions for the prior-bug pack, owner
rejections that override mechanical passes, and domain data to exercise (scripts, special
characters).

Sources: [TD*], [ET*], [BV*] in references/test-design.md; [RG*] in references/regression.md.
