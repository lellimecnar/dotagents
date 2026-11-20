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
- If the built-in way looks wrong for the situation, raise it with the owner rather than quietly building around
  it.

## What to report
When you propose or deliver something, name the mechanism you used ("the tool's built-in X", "standard library Y").
If anything is custom, state in one line what you checked and why existing paths do not fit. If no authorized
path remains after due diligence, finish independent work and report the verified blocker. Ask the owner only
for a decision or action they alone can supply; do not substitute an unauthorized approach.

## If you find a violation
When you notice something already built that reinvents a solved problem (yours or anyone's), do not extend it.
Report it: what it is, the built-in or standard replacement, and the cost of switching.
