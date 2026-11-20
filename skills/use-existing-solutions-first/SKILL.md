---
name: use-existing-solutions-first
description: Use always, before building anything. Find the built-in or existing solution first.
metadata:
  version: "1.0.0"
  status: active
  owner: maintainer
---

# Use existing solutions first (never reinvent the wheel)

This is a standing rule from the owner. It applies to every agent, every task and every domain: code,
infrastructure, configuration, workflows, documents, schedules, household systems, research methods. Breaking it
needs the owner's explicit authorization for that specific case, given in advance.

## The principle
A problem that has already been solved must not be solved again from scratch. Before you design, script, build
or improvise anything, you do the due diligence to find out how it is meant to be done, and you use that.
Custom work is for problems that are genuinely one-off: specific enough that no built-in mechanism, standard
practice or existing tool covers them.

Reinventing the wheel is not neutral. A home-made version of a solved problem usually misses the edge cases the
standard one already handles, adds something nobody else knows how to maintain, and fights the system it lives
in. The cost shows up later, as drift, breakage and rework.

## Due diligence, in this order
1. **The built-in way.** How does the tool, platform or service you are working in intend this to be done? Read
   its documentation and the help for the whole lifecycle of the thing (create, maintain, share, receive, update,
   remove), not just the first step. Most "missing features" are documented features you have not found yet.
2. **An existing standard, tool, service, library or pattern.** Is this a known problem with a known answer:
   an official integration, a widely used library, a community project, an established practice in the field?
3. **Only then, custom.** If nothing fits, say what you checked and why each option does not work, then build
   the smallest custom piece that fills the real gap, using the existing pieces for everything else.

When choosing among existing options, prefer in this order: official or supported; community or open source;
actively maintained forks of those; the owner's own forks; custom-built only when truly necessary. Prefer free and
self-hostable options before paid services.

## Judgment, not ritual
- Scale the search to the stakes. A two-line shell command does not need a literature review; a new subsystem,
  sync mechanism, scheduler, credential flow or integration always does.
- "Existing" includes what is already in this system: another agent's skill, a shared document, a service already
  running, a mechanism already configured. Reuse before you add.
- Before inventing a workaround around a built-in limitation, confirm the limitation and follow the upstream
  search in `due-diligence-before-deciding`. An existing, verified configuration workaround may be a supported
  path; discovering one does not authorize a patch, install or change outside the task.
- Before you present the owner with a choice between options, finish the search that the choice depends on. When
  the request is for a supported way to connect or sync two things, look in the vendor's own integrations or
  plugins collection and its issue tracker for that exact job first. An either/or built on an unsearched gap makes
  the owner decide between false options, and undoing that decision later costs far more than the search.
- "Existing" also means existing in the vendor's ecosystem, not only in the product itself: a capability a
  maintainer ruled out of the core product is often shipped by the same team as a separate integration.
- Never tell the owner something "can't be done" with a tool until you have read the vendor's own guide for that
  exact class of problem (multi-tenancy, access control, sync, sharing) and the tool's integration's config keys.
  A proposed fix that duplicates or mirrors data to get around a limit is a strong sign the search stopped early:
  the documented pattern usually composes existing pieces instead.
- If the built-in way looks wrong for the situation, raise it with the owner rather than quietly building around
  it.
- "Is there a way for me to see/hear/try X?" asks for an existing public resource (a vendor page, docs with demos,
  a third-party site), not for tooling you build or run. Search the web for it first; generating samples or demos
  yourself (scripts, API calls, spend) is custom work that needs the owner's yes.
- A decided tool stays decided: never swap in a different tool just because it happens to be installed (an old
  runtime found on a machine is not the chosen one). Ask before substituting, even for a "quick test".

## Verify before you advise, and check alternatives
Every claim and recommendation is checked before it is stated, with the alternatives named. This matters most when
you are about to say "it can't be done", "not yet" or "not worth it", or to hand the owner manual steps.
- **Check that the built-in or existing path exists before recommending against it.** Look at what the repo and
  environment already have (declared providers, configured integrations, live settings) and at the vendor docs.
  A negative recommendation is a claim that needs evidence.
- **Read live state before giving setup steps.** Query the real configuration (rules, defaults, installed apps) so
  the steps match what is actually set. Steps built from docs alone tend to miss the gate that really blocks
  (for example, a review requirement a merge tool still enforces).
- **Keep verified and unverified apart** in the same message. Do not present an inference (a permission name, a
  bypass mode, who owns an identifier) as fact: test it on one case, or ask the owner of that system.
- **Offer the alternatives with a recommendation**: what else could do the job, the cost of each, and which you
  would pick and why.
- **Retract fast.** When a check shows an earlier statement was wrong, say so in one line and give the corrected
  answer.

## What to report
When you propose or deliver something, name the mechanism you used ("the tool's built-in X", "standard library Y").
If anything is custom, state in one line what you checked and why existing paths do not fit. If no authorized
path remains after due diligence, finish independent work and report the verified blocker. Ask the owner only
for a decision or action they alone can supply; do not substitute an unauthorized approach.

## If you find a violation
When you notice something already built that reinvents a solved problem (yours or anyone's), do not extend it.
Report it: what it is, the built-in or standard replacement, and the cost of switching.
