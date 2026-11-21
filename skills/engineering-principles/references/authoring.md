# Authoring: how this document evolves

This is a living document. It should get clearer over time, not longer. A principles doc that
grows by appending a rule for every incident becomes a rule dump: nobody reads it, its rules start
to contradict each other, and agents follow the letter of whichever rule they found first. The
practices below exist to stop that.

## What a principle must have

Every `EP-nn` entry has the same fields, and an entry missing any of them doesn't go in:

- **Principle:** one or two sentences stating the idea, not an if/then rule.
- **Why:** the value or values it protects (readability, maintainability, testability, simplicity,
  safety) and the mechanism by which violating it causes harm.
- **Looks like:** signals that should make a reader look closer, with at most one or two labelled
  examples.
- **Not this:** the tensions and the cases where it does not apply. A principle with no "not this"
  is usually a decree in disguise.
- **Machine check:** the lint, type or test that catches its signals, or "judgment".
- **Revisit when:** the condition that would make it wrong or too costly.

Write in plain sentences that explain reasons. Avoid all-caps commands and absolute words used for
emphasis; if something truly is absolute (a security boundary), say why it is.

## When to add a principle

Add one only when all of these hold:

1. Real findings keep coming up that no existing principle explains, and the pattern has appeared
   in more than one change or project.
2. It can't be expressed as a clarification or example of an existing principle.
3. It protects one of the values, and you can state its tensions and revisit condition.
4. It is project-neutral. Something true only for one repo belongs in that repo's `AGENTS.md` as a
   project rule, mapped to the principle it applies.

If adding it would push `SKILL.md` past about 250 lines, merge or tighten something first.

## When to merge, split or retire

- **Merge** two principles when reviewers usually cite them together, confuse them, or the same
  finding could honestly cite either.
- **Split** a principle when findings citing it describe unrelated problems and fixers draw the
  wrong lesson from it.
- **Retire** a principle when it hasn't been cited in review for two quarters, or when a machine
  check now enforces all of it. A retired principle keeps its ID (IDs are never reused) and leaves
  a one-line pointer in the decision log.

## Examples stay few and illustrative

Examples help a reader see the idea; they also tempt readers to treat them as the boundary of the
rule. So:

- At most one or two examples per principle in `SKILL.md`, each labelled *Example*.
- Choose examples that show the principle, not a project's particulars. Rotate in a better example
  rather than adding a second one.
- A recurring specific case that keeps causing findings is a signal to sharpen the principle's
  wording or add a machine check, not to add an example.

## Measuring use and pruning

Reviewers cite `EP-nn` IDs in every finding, which makes the document measurable. Once a quarter,
count citations per ID across your PR reviews, for example:

```bash
gh api "repos/$REPO/pulls/comments?per_page=100" --paginate -q '.[].body' \
  | grep -oE 'EP-[0-9]{2}' | sort | uniq -c | sort -rn
```

(Include review bodies too, via `gh api repos/$REPO/pulls/$PR/reviews`, since findings outside the
diff live there.) Look for principles that are never cited (candidates to retire or merge), ones
cited constantly (candidates for a machine check), and findings citing no principle (a gap, or
noise).

## Promoting signals to machine checks

When a principle has a signal a machine can detect reliably, encode it, so review attention goes to
judgment:

1. Write the check with a message that names the principle and the fix (`EP-01: raw colour
   '#2E4560' duplicates token colors.navy[600]; use the token`).
2. Ship it as a **warning** and count the existing backlog.
3. Clean the backlog, ideally as class fixes in small, single-concern PRs.
4. Switch it to an **error** once the backlog is zero. Turning on an error over a known backlog
   makes CI red on day one and teaches everyone to ignore it.

The principle stays in this document with a pointer to the check; the check is how it is enforced,
not a replacement for the reason.

## Evaluating changes

Keep a small set of real past review comments with the expected handling (which principle, what
class, what fix level). Before merging a change to this document, run a reviewer and a fixer agent
against a few of them and confirm the change helps rather than hurts. A wording change that makes
agents cite the wrong principle is a regression.

## Decision log

Every change to a principle's meaning gets an entry in [decision-log.md](decision-log.md). Entries
are appended, never edited after acceptance; a later decision supersedes an earlier one by ID.
Format:

```markdown
## D-NNN Title
- **Status:** proposed | open (awaiting owner) | accepted | superseded by D-NNN
- **Date / decided by:** YYYY-MM-DD, name
- **Context:** what prompted this, with links to the findings or PRs.
- **Decision:** what changes, in one or two sentences.
- **Options considered:** for open decisions, each option stated neutrally.
- **Consequences:** what gets easier, what gets harder, what must change elsewhere.
- **Supersedes:** D-NNN, if any.
```

Editorial fixes (typos, a clearer sentence with the same meaning) need no entry.

## Who may change it

- **Any agent** may propose a change: open a PR against the repository that holds this skill, with
  the edit and a decision-log entry, and explain which findings motivated it.
- **The repository owner** reviews and merges. A proposal is not in force until merged.
- **Agents do not edit the installed copy** of this skill in place (for example with a skill-editing
  tool), even if their runtime would allow it. An in-place edit drifts from the shared source and
  silently changes the rules for one agent only.
- **Project rules** in a repo's `AGENTS.md` follow that repo's own process. When a project rule
  conflicts with a principle here, raise it as a decision for the owner rather than choosing
  silently (see D-003 for an example).

## Where it lives

This skill is one part of the owner's shared agent repository, which holds tool-neutral material
for any agent harness. Each harness loads it from a read-only checkout of that repository and keeps
it current with a scheduled pull; how is the harness's business, not this doc's. Keep the text
tool-neutral: harness-specific steps (task boards, tool names) belong in each agent's own
instructions. No agent should keep a same-named local copy, because a local copy shadows the shared
one. Each project repo's root `AGENTS.md` carries one line pointing agents to this skill rather
than restating it.
