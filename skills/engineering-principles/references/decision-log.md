# Decision log

Decisions about the engineering principles and how they are applied. Append new entries at the
end. Once an entry is accepted, it is not edited; a later entry supersedes it by ID. Open entries
are waiting for the owner and may be refined until decided. Format and process:
[authoring.md](authoring.md).

---

## D-001 Principles over decrees
- **Status:** accepted
- **Date / decided by:** 2026-11-14, owner
- **Context:** Agent instructions across a set of agents had grown into lists of if/then rules, often in
  capitals, with little explanation. Agents followed their letter, missed their intent, and had no
  way to handle cases the rules didn't anticipate. Contradictions crept in between rule sets.
- **Decision:** Every coding rule is stated as a principle with the value(s) it protects. Specific
  cases appear only as labelled examples. The shared document stays short and evolves by getting
  clearer, not by accumulating cases. Project-specific rules stay in each repo's `AGENTS.md` and
  are mapped to principles.
- **Consequences:** Agents are expected to exercise judgment and to explain it. Reviewers must cite
  a principle or project rule for every finding. Machine checks remain welcome, but each one names
  the principle it serves. Existing project rule sets (for example a repo's integrity standard) should
  be reframed around their principles over time; see [project-mapping.md](project-mapping.md).
- **Supersedes:** none.

## D-002 Findings are classes
- **Status:** accepted
- **Date / decided by:** 2026-11-14, owner
- **Context:** Fixes for review comments tended to change the quoted line only. The same class of
  problem stayed elsewhere and came back in later reviews; agents copied the uncorrected instances.
- **Decision:** A review finding, bug or lint error is treated as evidence of a violated principle.
  Reviewers state each finding as a class with a scoped search and the instances found. Fixers
  identify the principle, search for every other instance in scope before fixing, fix at the shared
  cause, add a guard, and report the evidence (EP-10, review and fix protocols). "Smallest change"
  means smallest *sufficient* change: no hard-coded one-offs, no fix-one-ignore-the-rest, and no
  rewrite for a minor bug (EP-11).
- **Consequences:** Fix rounds take somewhat longer and touch more files; review rounds should fall,
  and repeat findings should become rare. Reports must include queries and ruled-out hits.
- **Supersedes:** none.

## D-003 Out-of-scope instances are marked with a structured `@todo`
- **Status:** accepted
- **Date / decided by:** 2026-11-14, owner
- **Context:** a project's integrity standard handed off out-of-scope or risky fixes with a
  `@todo:` comment, while another of its rules forbade deferring without owner sign-off.
  An alternative that routed every such finding to the owner through a task card was rejected as
  too tied to one harness.
- **Decision:** a fixer or author who cannot safely fix an instance within the change's scope leaves
  a structured marker at the site, `@todo(EP-nn): <class in one sentence> — <what the fix should be>`, and lists the markers in the PR description. The
  project's tooling, whatever it is, may collect markers on a schedule and turn them into tickets.
  Markers are precise descriptions of owed work, written for a reader who has never seen the PR;
  known instances are never left unmarked.
- **Consequences:** the fix and review protocols use the marker; project rules that forbid deferral
  should be reworded so a structured marker counts as an accepted hand-off (see
  `project-mapping.md`). Harnesses that want tickets add a marker sweep.
- **Supersedes:** none.

## D-004 Naming literals is a judgment, not an exception list
- **Status:** accepted
- **Date / decided by:** 2026-11-14, owner
- **Context:** a project's literal-naming invariant ("no magic value appears inline... there is no
  third option"), read literally, forces names like `const ONE = 1`. A proposed fix replaced it with a list of exempt
  literals. The owner rejected both as rigid: sometimes naming a value helps, sometimes it hurts,
  and a fixed exception list is just another rule.
- **Decision:** EP-01 governs. Give a value a name when the name carries knowledge a reader needs
  (which decision it represents, so it can change in one place) and leave it inline when the
  literal is clearer than any name. Reviewers who ask for a name, or for removing one, say which
  value is at stake (readability, maintainability). Lint may flag candidates, but its message is a
  prompt for judgment that cites EP-01, not a verdict.
- **Consequences:** propose restating such project invariants as this principle rather than an
  absolute (see `project-mapping.md`); tune any literal lint to warn, not block.
- **Supersedes:** none.

## D-005 Review checks fit with the live system, not only the pattern
- **Status:** accepted
- **Date / decided by:** 2026-11-14, owner
- **Context:** a live test review approved a correct fix while the surrounding script still targeted
  a retired platform; running it would have undone a migration. Both author and reviewer searched only
  for repeats of the reported bug pattern.
- **Decision:** reviewers and fixers check that touched code still matches the system as it runs today
  (services, auth, configuration, completed migrations and decisions). Stale assumptions are a class
  finding with all instances listed.
- **Consequences:** review-protocol section 2 and fix-protocol section 9 gain this step.
- **Supersedes:** none.

## D-006 The live-fit check uses what the repo records
- **Status:** accepted
- **Date / decided by:** 2026-11-14, owner
- **Context:** in the next test the reviewer read D-005 as "query the live service", had no credentials,
  and declined the check. The retirement it should have caught was not recorded in the repo either.
- **Decision:** the live-fit check uses everything readable (decision records, docs, configuration,
  history); live access is a bonus, never a precondition. Projects record migrations and retirements in
  their decision records so the check has evidence.
- **Consequences:** review-protocol section 2 and fix-protocol section 9 reworded; version 0.2.1.
- **Supersedes:** none (clarifies D-005).

