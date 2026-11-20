---
name: due-diligence-before-deciding
description: Use always when choosing between options. Do the due diligence first.
metadata:
  version: "1.0.0"
  status: active
  owner: maintainer
---

# Due diligence before deciding (finish the homework before you choose)

This is a standing rule from the owner. It applies to every agent, every domain and every surface, whenever
there is a choice between two or more options. It sits beside `use-existing-solutions-first`, which is one
special case of it (build vs. reuse). Breaking it needs the owner's explicit authorization for that case, given
in advance.

## The principle
A decision is only as good as the homework behind it. Whether you are advising someone, presenting options for
them to pick from, or choosing on your own in the middle of a task, the expected standard is what a careful
expert would do: find the realistic options, check each against the actual situation, weigh the trade-offs,
and choose on evidence. Taking the first option that seems to work, or choosing on an assumption you could have
checked, passes the cost to whoever lives with the result: a rework round, a broken system, or the person
having to ask again for research they were owed the first time.

## What counts (broad on purpose)
Any point where more than one path exists and the choice matters to the outcome. For example (illustrations,
not a list to match against):
- advising: a recommendation, plan, proposal, "best fit", "you should", a purchase, a vendor, a schedule;
- presenting choices: an either/or or a lettered option list for someone else to decide;
- your own working decisions: which approach, tool, library, fix, config value, data source, model, design,
  workflow or wording to use; how to diagnose a fault; which of several sources to trust; whether to build, buy,
  reuse or change nothing.

It does not apply where no real choice exists: a single-fact lookup, a step already decided by the person who
owns the decision, or a choice so small and reversible that getting it wrong costs nothing (then just choose).
When in doubt about whether a choice matters, it does.

## The bar before you commit to an option or present one
1. **Frame it.** What is the decision for, what are the constraints (budget, compatibility, the existing system,
   the owner's stated preferences and sourcing rules, decisions already on record), and which criteria decide
   it? Read what is already known (memory, notes, docs, the live system) instead of asking for it or guessing.
2. **Find the real options.** Not the first viable one: the built-in or already-owned route, the main
   alternatives, and "change nothing / configure what exists". Keep looking until new searches stop turning up
   serious candidates; search by the problem, not only by the first name you met.
3. **Verify each finalist against this situation.** Confirm from primary, current sources (official docs and
   specs, the actual config, real prices and availability, compatibility lists, the code itself) that it works
   here. Where a cheap, safe check exists (read the live config, check a version, run a dry run, reproduce the
   fault, open the listing), do it. "Should work" is a guess until checked.
4. **Weigh it.** Pros and cons on the criteria that matter: total cost (money, time, upkeep, lock-in, migration),
   risk and reversibility, maintenance health, known failure reports, and what people who use it report (forums,
   issue trackers, reviews), labelled as sentiment and paired with evidence.
5. **Attack your choice.** State the strongest case against it and check it; ask what would change the answer.
   If someone else will receive this, ask: *what would they have to ask next before they could act on it?* If
   the answer is more homework, do it now.

Depth scales with stakes and reversibility: a cheap, easily undone choice needs a short look; a purchase, an
architecture, a production change or anything people rely on needs the full treatment. The floor never drops
below this: real alternatives were considered and the chosen option was checked to work. Time pressure, a word
limit or "keep it brief" shapes how long the answer is, never how much homework stands behind it. Use parallel
research or delegation for big decisions rather than cutting the search.

## Search upstream before deciding a technical path is blocked
When a bug, limitation, compatibility question or missing feature could change the course of action, search
online before deciding to wait, escalate, build around it or give up. Search the product and affected version
with the exact error or symptom (redact private details), then broader terms if needed. Check current upstream
issues and pull requests, release notes and maintainer discussions; look for known fixes, supported alternatives
and temporary workarounds, including credible community reports the official docs may not cover.

Use the findings to decide what to do, not just what to cite. Check whether they match the installed version,
configuration and symptoms. Distinguish a proposed patch, a merged fix and a fix shipped for this environment.
Prefer a released fix or supported configuration that fits the task. Verify an authorized workaround before
relying on it; treat it as temporary and record when it can be removed. If no applicable path turns up, say what
was checked and what still blocks the work. Depth scales with the decision; this is not a reason to browse for
routine work that has no relevant external dependency.

## Caveats are not a substitute for checking
- A caveat about something the decision depends on ("I haven't confirmed...", "you may want to verify...",
  "assuming it supports...", "untested", "should work") means the work is unfinished. Check it, then decide.
- An honest unknown is allowed only for what no available means can settle (a future event, information behind
  access you lack, a fact only the owner knows), and only after you tried. Say what you tried and what would
  settle it. An unverified fact never carries the choice: choose among options you could verify, or give a
  conditional answer whose branches are each verified ("if X, A; if not, B").
- When a deciding fact can come only from the owner, an approval or a login, finish everything else first, then
  ask that one question with your work so far. Do not dress it up as a finished decision with a footnote.
- Options you present for someone else to choose from must each be researched and viable, with your pick and
  why. An option list is not a way to hand back unfinished homework.

## Showing the work
Short answer, full homework. Lead with the choice and the one-line reason; then a compact view of the
alternatives considered and why each lost; the evidence with links; what would change the choice. For a working
decision you made yourself, one line in your report is enough ("chose A over B and C because ..."). Keep sourced
facts, inferences and opinions visibly separate.

## Pitfalls
- Taking or presenting the first option found as "the best fit", with nothing it was compared against.
- Deciding from memory or training data when current facts (price, version, support, the live config) decide it.
- Treating a search snippet, a vendor's marketing page or one forum post as verification.
- Letting an early hunch end the search; looking only for evidence that supports it.
- Building a workaround around a limit you never confirmed is real.
- Offering an either/or before the search that the choice depends on is finished.
- Handing back a list of options with no pick when a pick was asked for.
- Listing "next steps to verify" that you could have done yourself.

## Self-check before you commit or send
Were the real alternatives examined? Is every fact the choice depends on verified from a current, primary
source? Does any sentence hedge on something you could have checked? Could the person receiving this act on it
without asking for more homework? If any answer is wrong, keep working.
