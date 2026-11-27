# Review protocol

How a reviewing agent evaluates a change and reports what it finds. The goal of a review is
code health: approve a change once it clearly improves the codebase, even if it isn't perfect, and
never approve one that makes it worse. Every finding should teach the author a principle and
describe a class of problem, so that one round of fixes removes the problem everywhere rather than
on the quoted line.

Your SOUL or profile defines the lane you work in (which board, which tools, who gets the verdict).
This document defines *how* to review inside it.

## Contents

1. Before you read the diff
2. Reading the change
3. Turning a concern into a finding
4. The scoped variant search
5. Comment format
6. Blocking and non-blocking
7. Later rounds
8. Verdict
9. Posting one GitHub review
10. Hand-off to the task tracker
11. Credits

## 1. Before you read the diff

- Read the task in the owner's own words, the acceptance criteria, and any linked spec or plan. A
  correct implementation of the wrong thing is the most expensive defect, so check this first.
- Read the repo's `AGENTS.md` and the nearest leaf `AGENTS.md` for every touched area. Those are
  the *project rules* you may cite alongside the `EP-nn` principles.
- Check out the change at its current head, note the head SHA, and rerun the project's checks
  (tests, lint, typecheck, build) yourself. An author's report is a claim to verify, not evidence.

## 2. Reading the change

Read the change and its surroundings, not only the diff. A diff shows what was added; it cannot
show the existing helper that should have been reused, the sibling file with the same bug, or the
caller that depends on the behaviour being changed.

For each touched unit, look at:

- **Its neighbours:** sibling files, the module's public interface, existing utilities and
  components that already do something similar (EP-01, EP-02).
- **Its callers:** who depends on any behaviour this change alters (EP-09).
- **Its tests:** do they test the changed behaviour directly, and would they fail if it broke (EP-08)?
- **The reader's view:** could a capable new junior understand the file without opening ten others
  (EP-04, EP-05)? Is there machinery the problem doesn't need (EP-06)?
- **Its fit with the system as it runs today:** the code a change touches carries assumptions about
  the world: which services exist, how they authenticate, what configuration and names are live,
  which integrations were retired or replaced. Check those assumptions against everything you can
  read: the repo's decision records and docs, its configuration, its recent history, and live state only
  if you happen to have access. Missing credentials for a live service are never a reason to skip this
  check, because the repo itself usually records what is current. A decision record, doc or recent
  change that contradicts the code is evidence the code is stale. An
  approved change that leaves its surrounding code describing a world that no longer exists is not
  done: running that code can silently undo a migration (EP-09, EP-01). Stale assumptions are a
  class: name it ("this module still targets the retired X") and list every instance, not only the
  lines in the diff. *Example:* a fix to how a sync script finds a webhook, inside a script whose
  variable names, comments and auth method still target a platform the project already left.

Keep a short **declined to judge** list: things you noticed but set aside, with the reason (outside
the task, generated code, an exception documented in a decision record). Nothing you set aside is
dropped silently; the list goes in the review body.

## 3. Turning a concern into a finding

A concern becomes a finding only when you can cite what it violates:

- an `EP-nn` principle from this skill, or
- a project rule, by its ID and file (for example `INV-3, AGENTS.md`).

Do not invent rules. If something bothers you and you cannot cite either, it is a `question`, or
it is a preference to leave out. If you believe a real principle is missing from this document,
say so in the review body as a proposal for the owner, separate from the findings (see
[authoring.md](authoring.md)).

Then state the **class** in one sentence: the general problem, not the line. A useful shape is:

> `<construct>` appears where `<principle>` requires `<property>`.

*Example:* "Plan-gating conditions are written inline at call sites where EP-01 requires one
shared, tested predicate." The class sentence is also the starting point for the search.

## 4. The scoped variant search

Before posting a finding, spend a few minutes checking whether it is a class with more than one
member. You are not doing the fixer's exhaustive search; you are giving the fixer a calibrated
starting point and showing the problem's real size.

1. Write a search that matches the instance you saw (calibration: if it doesn't match, your class
   statement is wrong).
2. Widen it one step: related names, sibling files, other call sites of the same API, other
   languages in the repo that encode the same knowledge.
3. Skim the hits, keep the real instances, and note how many were noise.

Record the search exactly as you ran it and the instances you confirmed. "No other instances
found" is a valid result, but only with the search attached.

## 5. Comment format

Each finding uses [Conventional Comments](https://conventionalcomments.org/):
`label (decorations): subject`, followed by fixed fields. Keep the subject to one line.

```text
issue (blocking, EP-01): Plan-gating condition re-implemented inline in CheckoutPage.

finding: R1-03
principle: EP-01 One source of truth (also project rule INV-2, AGENTS.md)
class: Plan-gating conditions are written inline at call sites instead of one shared, tested predicate.
instances: web/src/checkout/CheckoutPage.tsx:42 (this PR); web/src/billing/Upgrade.tsx:17; api/billing/gate.py:88
search: rg -n "plan ===? '(pro|team)'" web api  (9 hits: 3 real, 6 test fixtures)
why: The copies already disagree: Upgrade.tsx ignores the trial period, so trial users see the paywall.
fix direction: One predicate (e.g. hasPaidFeatures(user, now)) with tests for every plan and trial edge, used at all three sites.
```

- **Labels:** `issue`, `suggestion`, `question`, `nitpick`, `todo`, `chore`, and `praise` when it is
  specific and useful. Use `question` when you are not sure a problem exists.
- **Decorations:** `blocking` or `non-blocking`, the principle or project-rule ID, and optionally
  `security` or `test`.
- **finding:** a stable ID, `R<round>-<nn>`, assigned when first raised and kept for the life of the
  finding, across rounds, on GitHub and in any task tracker. A finding raised in round 1 is still `R1-03`
  in round 3. Identical IDs let everyone see which findings are new and which are carried over.
- **fix direction:** where the fix belongs (which shared unit, which level), not a full patch. It is
  the author's job to fix the change; your job is to make the class and the right level clear.

Findings on lines outside the diff (a sibling file, an existing helper) still use this format; they
go in the review body because GitHub only accepts inline comments on diff lines.

## 6. Blocking and non-blocking

**Blocking** means the change should not merge until it is resolved:

- correctness or safety defects, including data-integrity and security issues;
- the wrong problem solved, or an acceptance criterion not met;
- a project-rule violation;
- a principle violation that makes code health worse (new duplication of knowledge, a new
  untestable unit, a hack where a cause fix was needed);
- changed behaviour without a test that would catch it breaking;
- a prior-round finding fixed only as an instance (see below).

**Non-blocking** means the change improves code health even without it, but the codebase would be
better with it: a clearer name, a missing example in an otherwise good docblock.

Non-blocking does not mean optional to track. The author either fixes a non-blocking finding in
the current round or leaves a structured marker (`@todo(EP-nn): <class in one sentence> — <what the fix should be>`) at each instance and lists them in
the PR, so tooling can turn them into tickets (decision D-003). It is never silently dropped. Your
approval lists any open non-blocking findings so they stay visible.

## 7. Later rounds

From round 2 on, start with the previous findings before reading anything new:

1. For each prior finding, rerun its recorded search (or the author's widened one). Check that
   every listed instance is fixed, that the fix sits at the shared cause rather than at each call
   site, and that a guard (test, lint rule or type) now covers the class.
2. Mark each prior finding `verified`, `still open`, or `disputed` (author pushed back with
   evidence; decide on the evidence, and escalate if you still disagree).
3. Only then review what changed since the last round, assigning new IDs (`R2-01` and so on).

Do not re-raise a finding the owner waived unless the new code makes it worse. Do not hold a
change to a higher bar in round 3 than you set in round 1; new concerns late in the cycle should be
real, not a moving target.

## 8. Verdict

- **Approve** when no blocking finding is open, every prior finding is verified as a class fix, and
  the checks you reran pass at the head you reviewed. List any open non-blocking findings.
- **Request changes** when any blocking finding is open. Include every finding, not just the first
  few; the author should be able to fix all classes in one round.
- **At the round cap**, do not start a fourth round: approve with the non-critical findings listed
  as Known limits, or ask the lead about a critical disagreement. Only an intent question that no
  recorded rule settles goes to the owner, in one decidable sentence.

## 9. Posting one GitHub review

Post the whole review as a single GitHub review at the head SHA you checked, with inline comments
for findings on diff lines and the rest in the body. One review keeps the verdict, the findings and
the SHA together.

```bash
# Confirm the head hasn't moved since you checked it out.
gh pr view "$PR" --repo "$REPO" --json headRefOid -q .headRefOid

# review.json: event is APPROVE, REQUEST_CHANGES or COMMENT.
# "line" is the line number in the new file; side RIGHT means the new version.
cat > review.json <<'EOF'
{
  "commit_id": "<head sha>",
  "event": "REQUEST_CHANGES",
  "body": "Summary, verdict, findings outside the diff, prior-round status, declined-to-judge list.",
  "comments": [
    { "path": "web/src/checkout/CheckoutPage.tsx", "line": 42, "side": "RIGHT",
      "body": "issue (blocking, EP-01): ...full finding block..." }
  ]
}
EOF

gh api --method POST "repos/$REPO/pulls/$PR/reviews" --input review.json
```

- If the head moved, re-check out the new head and review again; a review against a stale SHA
  approves code nobody looked at.
- A multi-line comment adds `start_line` and `start_side`. A comment on a line outside the diff
  fails with a 422 error, so move that finding into `body`.
- GitHub does not let an identity approve or request changes on its own PR; the reviewer posts with
  its own identity (a separate account or GitHub App token), as its profile is configured.
- Build `review.json` with a JSON tool (for example Python's `json.dump`) when bodies contain quotes
  or newlines, rather than hand-escaping.

## 10. Hand-off to the task tracker

If your harness tracks work outside the PR (a board, tickets, a queue), mirror the PR verdict there
with the same finding blocks and IDs: changes needed, approved at a specific commit, or stuck after
the round cap with Known limits or the disagreement named for the lead. How to do that is harness-specific and
lives in your own agent instructions, not in this shared doc.

Reviewers do not merge. Your agent instructions name who does.

## 11. Credits

The comment grammar is from Conventional Comments. "Never invent rules", stable finding identity
across rounds, and severity honesty are adapted from Qodo PR-Agent's reviewer prompts (MIT). The
declined-to-judge list and verify-before-trusting stance are adapted from obra/superpowers'
code-review skills (MIT). Code-health-over-perfection follows Google's Engineering Practices review
guide (CC BY 3.0). The scoped search is a lightweight form of variant analysis as described by
GitHub Security Lab and Trail of Bits, re-expressed in our own words.
