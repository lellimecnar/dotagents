---
name: engineering-principles
description: Use when writing, reviewing or fixing code. Principles plus review and fix-the-class protocols.
metadata:
  version: "0.2.2"
  status: draft
  owner: maintainer
---

# Engineering principles

One shared set of coding principles for every agent that writes, reviews or fixes code, in any
tool, harness or repository. It tells you what good code protects and why, so you can make the
right call in situations no rule anticipated. It is deliberately short. Project-specific rules
(a repo's invariants, ports, branch names, tooling) live in that repo's own `AGENTS.md`; when
you work in a repo, read them too and treat them as binding *project rules* that apply these
principles locally.

## The values

Every principle exists to protect one or more of these. When a principle and its value disagree in
a specific case, the value wins, and you say so in the PR or commit.

| Value | What it means in practice |
| --- | --- |
| **Readability** | A reader works out what code does and why from the file in front of them, quickly. |
| **Maintainability** | One change in requirements means one change in code, in one predictable place. |
| **Testability** | Each behaviour can be checked directly, in isolation, cheaply. |
| **Simplicity** | The least machinery that solves today's real problem. |
| **Safety** | Wrong states, data loss and regressions are hard to produce and easy to detect. |

## How to use these principles

These are principles, not decrees. Nothing below is an if/then rule to apply mechanically; each
entry states what it protects so you can judge the case in front of you. Examples are labelled
*Example* and illustrate the idea; they do not mark its edges.

- **Name the principle, then the instance.** Every review comment, lint message and fix
  description names the principle (`EP-nn`) or project rule it serves. A concern you cannot tie to
  either is a preference: ask a question or leave it out. This keeps review honest and teachable.
- **Treat every finding as a class.** A comment on one line is evidence that a principle is being
  violated. Before fixing, state the general problem in one sentence, search for its other
  instances, and fix it where the instances share a cause, often one util or component used
  everywhere. Fixing only the quoted line leaves the cause in place to reappear (EP-10).
- **Smallest sufficient change.** Small means "don't rebuild the system for a minor bug". It never
  means a hard-coded one-off, and never means fixing one instance while leaving its siblings. The
  right change removes the whole class at the right level, and no more (EP-11).
- **Overengineering signals a misunderstood principle.** If you reach for a layer, pattern or
  option the problem doesn't need, go back to the value it was meant to serve (EP-06).
- **Resolve tensions out loud.** Principles pull against each other by design (shared code vs
  coupling, locality vs reuse, building for today vs ease of change). Name the tension, pick by the
  value at stake, and record the reason in one sentence where the next reader will see it.
- **Evidence over taste.** "Simpler", "faster" and "cleaner" need evidence: a test, a benchmark,
  fewer call sites, an eliminated bug class. Without evidence, leave working code alone.
- **Safety outranks tidiness.** Data integrity, security and irreversible operations come before
  speed, consistency and polish. When you cannot verify a change is safe, understand more first or
  hand it off.
- **The new-junior test.** Before you finish, reread each touched file as a capable new junior
  would: can they understand it from this file alone, skim it, and find the "why"? If not, keep going.

## The principles

### EP-01 One source of truth for each piece of knowledge
- **Principle:** each rule, fact, value or decision has one authoritative home; everything else refers to it, and values that encode a decision carry a name.
- **Why:** maintainability and safety. Copies drift, and a fix applied to one copy silently misses the others. A name records which decision a value represents, so it can change in one place.
- **Looks like:** the same condition, mapping, regex or threshold written several slightly different ways; a literal whose meaning you have to guess (`86400`, `'#2E4560'`); two docs stating one fact; one logical change needing edits in many files. *Example:* a plan check repeated in six files, two of which forget the trial period, becomes one tested `hasPaidFeatures(user, now)`.
- **Not this:** code that merely looks alike but encodes different knowledge. Ask: if one changes, must the other? If not, keep them apart (EP-02). Generated or cached copies with one source are fine. Self-evident literals (`0`, `1`, an empty string) usually need no name; project rules may say more.
- **Machine check:** duplicate-code detectors, magic-number and raw-colour lints, doc-drift checks, where the project has them.
- **Revisit when:** consolidating would force unrelated callers to change together.

### EP-02 Model concepts, not appearances or guesses
- **Principle:** shape shared code around what things *are and mean*, once that meaning is visible; components, functions and variants follow semantics, not looks.
- **Why:** maintainability and readability. Things that mean the same thing should change together; things that merely look alike will diverge. A wrong abstraction costs more than duplication, because callers bend it with flags until nobody dares touch it.
- **Looks like:** a new component about 90% identical to an existing one (probably a variant or a composition); several hand-rolled versions of one UI element differing in small, unintended ways; boolean or mode parameters used by a single caller; names like `BlueButton` or `HomePageCard`. *Example:* `ArticleCard` and `EventCard` become one `ContentCard` with a metadata slot, while a "variant" that only adjusts one screen's spacing is layout, which belongs to the caller.
- **Not this:** abstracting from a single instance "for future reuse", or merging things that only share a shape. When a shared unit has decayed into flags, inline it back into callers, then re-extract the real concept. Prefer composition when callers need different structure; use variants for different states of one structure.
- **Machine check:** mostly judgment. Projects may police token or variant growth (duplicate-value or location-named tokens).
- **Revisit when:** a variant set keeps growing with caller-specific options.

### EP-03 Cohesive, deep modules with narrow, one-way boundaries
- **Principle:** group what changes for the same reason; give each module a small, deliberate interface that hides its decisions; let dependencies point one way.
- **Why:** maintainability, testability, safety. A change stays inside one module, each module can be understood and tested alone, and hidden decisions can change without rippling outward.
- **Looks like:** files that can only be named with "and", "Utils" or "Manager"; deep imports into another module's internals; import cycles or a lower layer importing a higher one; callers that must know internal call order or data shapes; pass-through wrappers that add a layer but no abstraction. *Example:* a UI button that imports app analytics instead of exposing `onPress` for the app to wire.
- **Not this:** splitting purely to satisfy a size cap, which produces shallow fragments and scatters logic (EP-04). Adding interfaces or layers for a single implementation "in case we swap it" (EP-06). Size caps are signals to look for lost cohesion, not targets.
- **Machine check:** import-boundary and cycle lints, size and complexity caps (as warnings that prompt a look).
- **Revisit when:** a boundary is crossed in most changes, which means it sits in the wrong place.

### EP-04 Readable in place: intent-revealing names, local behaviour, plain over clever
- **Principle:** a unit's behaviour should be obvious from the unit itself; names carry meaning, and the plainest construction wins unless a harder one is demonstrably better.
- **Why:** readability. Code is read far more than written, and every jump to another file costs context, for agents in tokens and errors. A good name lets the reader skip the implementation.
- **Looks like:** understanding one function requires opening many files; behaviour wired at a distance through registries or global buses with no pointer at the call site; generic names (`data`, `handle`, `process`); a name that misstates behaviour (`getUser` that writes); dense one-liners where a loop would read plainly; an optimisation inlined in business logic with no explanation. *Example:* a binary search kept for a measured hot path lives in `lineAtOffset()`, whose docblock says why the obvious scan was rejected and cites the benchmark.
- **Not this:** inlining shared logic to gain locality. Extract it into a well-named, documented unit instead, so the call site reads correctly without being opened. Standard idioms of the language or codebase are not "clever". Short names are fine in tiny scopes.
- **Machine check:** naming lints where available; otherwise judgment.
- **Revisit when:** readers keep asking the same "what does this do?" about a pattern the codebase considers idiomatic.

### EP-05 Document the contract and the why, for a new junior
- **Principle:** every file and every exported or shared unit explains its purpose, contract and non-obvious constraints, as a senior engineer would for a capable new junior.
- **Why:** readability and safety. Code cannot record why it is this way, which constraints it relies on, or what was tried and rejected. Undocumented constraints get rediscovered the hard way.
- **Looks like:** exported functions or components with no docblock (purpose, inputs, output, errors, an example); a file with no header saying what it is for; ordering requirements, units or workarounds with no recorded reason; comments that narrate the code (`i++ // increment`) or have drifted from it.
- **Not this:** commenting *how* the code works line by line; prefer making the code clearer. Inline comments are for *why* and non-obvious *what*. Comments are code: update or delete them in the same change.
- **Machine check:** docblock-coverage lints on exported symbols, where available.
- **Revisit when:** docblocks become boilerplate that nobody reads; fix the template, not the principle.

### EP-06 The least complexity that solves today's known need
- **Principle:** build for the problem you can see now, and keep the code easy to change for the one you can't.
- **Why:** simplicity and maintainability. Speculative capability is usually built wrong and is paid for on every read. Ease of change (clear names, tests, deep modules) is what makes waiting safe.
- **Looks like:** options, plugin points or generic parameters no caller uses; an interface with one implementation "so we can add SMS later"; factories and strategies with one concrete use; special cases and flags multiplying; a reviewer who needs the author to explain the design.
- **Not this:** skipping refactors, tests or clear boundaries in the name of simplicity; those are what keep code malleable. Decisions that are expensive to reverse (data schemas, public APIs, security boundaries, destructive migrations) deserve more forethought, not less.
- **Machine check:** judgment; unused-export and dead-code tools catch some residue.
- **Revisit when:** the same extension point has been added and removed more than once.

### EP-07 Make wrong states hard to represent
- **Principle:** choose data types so invalid combinations cannot be constructed, and parse untrusted input once at the boundary into a trusted type.
- **Why:** safety. The type system or validator then enforces the rule everywhere, and later code cannot quietly skip a check.
- **Looks like:** several booleans or optional fields that together encode one state, some combinations meaningless; the same validation repeated across layers; non-null assertions, casts, or "can't happen" comments; stringly-typed IDs where a union or branded type fits. *Example:* `{status:'error', error}` | `{status:'success', data}` replaces independent `isLoading`, `isError`, `data?`, `error?` fields.
- **Not this:** type gymnastics that hurt readability. Use the simplest type that rules out the states that actually cause bugs. Shell scripts and prose repos apply this as early validation and fail-fast (`set -euo pipefail`, checking inputs before acting).
- **Machine check:** strict compiler settings, schema validation at I/O boundaries, lints against casts and non-null assertions.
- **Revisit when:** the type gets harder to read than the bugs it prevents.

### EP-08 Design every behaviour to be tested directly
- **Principle:** keep decisions in units that take values and return values; keep side effects in a thin outer shell; extract logic that can only be tested through its parent.
- **Why:** testability, and through it safe change. Direct tests are cheap, fast and precise; logic testable only through a component, handler or database tends to go untested. Extracted logic can also be documented and reused.
- **Looks like:** business rules inside a UI component, route handler or SQL-calling method; clocks, globals or env read deep inside logic; tests mocking five things to check one condition; a bug fix with no test that would have caught it; tests asserting implementation details. *Example:* an inline "unread this week" calculation becomes `selectUnreadThisWeek(items, log, now)` with table-driven tests.
- **Not this:** dependency-injection frameworks or interfaces added only to enable mocking; passing values in is usually enough. The shell still needs a few real integration or end-to-end checks.
- **Machine check:** coverage on changed lines; a failing-first test for each fix.
- **Revisit when:** tests break on harmless refactors, which means they test the wrong layer.

### EP-09 Understand before you change: fix causes, respect fences
- **Principle:** find out why a defect happens, and why odd-looking code exists, before changing either.
- **Why:** safety and maintainability. A patch where a symptom shows leaves the cause to produce the next symptom. Strange code (a retry, a sleep, an ordering) often encodes a lesson someone paid for, and other code may depend on behaviour nobody promised.
- **Looks like:** special-casing the failing input or caller; a `catch` that swallows an error, or a retry that hides a race; a null check where the value "shouldn't be null" with no investigation; removing a guard because "it doesn't seem to do anything"; changing output order, defaults or error text in a shared API without searching its consumers.
- **Not this:** keeping everything forever. Once the reason is understood and no longer applies, remove it and record why in the commit. In an emergency, mitigate first, label it mitigation, and report the cause fix as owed work.
- **Machine check:** judgment, supported by `git blame`, linked issues, and a test that captures what you learned.
- **Revisit when:** investigation cost routinely dwarfs the change; then the code needs better docs (EP-05).

### EP-10 Every finding is a class, not a one-off
- **Principle:** a review comment, bug or lint error on one line is evidence of a violated principle; name the principle, find its other instances in scope, and fix at the level that prevents recurrence.
- **Why:** maintainability and safety at agent scale. Agents copy whatever patterns exist, so a bad pattern anywhere spreads everywhere. Partial fixes are how the same bug returns through a different path.
- **Looks like (violations):** a fix that changes exactly the quoted line while a search still finds siblings; the same review comment on several PRs; a local workaround where the shared thing should be fixed or created; a fix with no test, type or check to stop the class returning. *Example:* "this hex colour should be a token" leads to a search across the package, 23 raw values replaced, and a lint rule so a 24th cannot land.
- **Not this:** a licence to rewrite. Class thinking sets the *scope*; EP-11 sets the *size*. Not every comment is a class: a typo is a typo. If the honest answer to "which principle?" is "none, it's taste", it isn't a finding.
- **Machine check:** the fix protocol's evidence (queries, hits, guard); repeat findings tracked by principle ID.
- **Revisit when:** class searches routinely return only noise; tighten how findings are stated.

### EP-11 Smallest sufficient change, and leave it better within bounds
- **Principle:** make the smallest change that fixes the problem everywhere it occurs, at the right level; improve what you touch only when the fix is a real principle violation, quick and safe.
- **Why:** simplicity and safety. Too small (a hack, a one-off, one instance of many) leaves the problem standing; too big (a rewrite for a minor bug) creates unreviewable risk. Bounded cleanup pays down decay without derailing the task.
- **Looks like:** *too small:* magic-value patches, a copied block with one tweak, a permanent "temporary" hack; *too big:* a bug fix that also restructures modules, renames widely, or mixes refactoring and behaviour in one commit. *Example:* a date bug in two of seven hand-rolled formatters becomes one commit extracting a shared, tested formatter used by all seven, then one commit fixing the bug in it.
- **Not this:** taste-driven churn. A class fix touching many files is still proportional when every site is the same instance and the change is mechanical. Unrelated cleanup goes in its own commit. Larger or riskier findings are reported, not silently fixed or silently parked.
- **Machine check:** diff size and commit separation are visible to the reviewer; judgment otherwise.
- **Revisit when:** reviews keep splitting the same kind of change differently.

### EP-12 Consistency with the codebase, guarded by machines
- **Principle:** follow the codebase's established patterns, and turn any principle with a detectable signal into a check whose message names the principle and the fix.
- **Why:** readability and safety. Consistent code is read with knowledge the reader already has, and agents amplify whatever they see. Machines catch signals tirelessly, which frees review for judgment.
- **Looks like:** a second fetch wrapper, date helper or state library next to the established one; a principle flagged again and again in review with no automated check; lint errors that say what is wrong but not why or how to fix it.
- **Not this:** consistency with a bad pattern. When the convention itself violates a principle, change it deliberately as a class fix rather than copying or quietly forking it. Not everything can be mechanised: EP-02 and EP-09 need judgment.
- **Machine check:** this principle is about adding them. New checks ship as warnings, the backlog is cleaned, then they become errors.
- **Revisit when:** a check is routinely suppressed, which means it is wrong or mis-scoped.

## Roles

- **Writing code:** apply the principles while you work, and run the new-junior test before handing off.
- **Reviewing:** follow [references/review-protocol.md](references/review-protocol.md). Read beyond the
  diff, cite a principle or project rule for every finding, and state each finding as a class with a
  scoped search behind it.
- **Fixing review feedback:** follow [references/fix-protocol.md](references/fix-protocol.md). Group
  findings by class, search before fixing, fix at the shared cause, add a guard, and report the
  evidence.

## References

- [references/review-protocol.md](references/review-protocol.md): reviewer procedure, comment format, verdicts, posting to GitHub.
- [references/fix-protocol.md](references/fix-protocol.md): fixer procedure for treating findings as classes.
- [references/authoring.md](references/authoring.md): how this document changes, and who may change it.
- [references/decision-log.md](references/decision-log.md): decisions about the principles, append and supersede only.
- [references/project-mapping.md](references/project-mapping.md): how to map a project's own rules to EP IDs, and find the gaps.

## Credits

Synthesised from public engineering literature (Hunt and Thomas, Metz, Ousterhout, Parnas, Fowler,
Beck, Google Engineering Practices (CC BY 3.0), Bernhardt, Minsky, King, Gross). The protocols
re-express ideas from obra/superpowers and Qodo PR-Agent (both MIT), Conventional Comments, and the
Trail of Bits variant-analysis method (described in our own words; see each reference's credits).
