---
name: science-plausibility-review
description: Use when reviewing science/tech plausibility in fiction. Smallest fix.
metadata:
  version: "0.1.2"
  status: draft
  owner: maintainer
---

# Science plausibility review for fiction

How a science reviewer checks the physics, spaceflight, planetary science, astrobiology, biology,
cybernetics and prosthetics in a manuscript, and where real science ends and the story's sanctioned
exceptions (magic, precursor technology, the numinous) begin. The goal is a **legible contract**, not
universal realism: ordinary events obey ordinary science, extrapolated technology pays consistent costs,
and exceptions stay inside their declared scope. Report the smallest story-preserving fix; clarity beats
technical display. Sources `[n]` resolve in `references/sources.md`.

## When to use

Any prose review that involves physics, spaceflight, planetary science, biology, medicine, engineering,
cybernetics or prosthetics, or the boundary between science and established magic. Genre sets the
precision: hard SF is held close to known principles; science fantasy may break them where canon says
so [16][17].

## Authority order

1. The project's binding constraints. Never flag an exact-text lock, decided no-op, protected span or
   withheld-lore omission as an error.
2. Pin the revision. Read the full chapter (not an excerpt), its outline/notes and relevant continuity.
3. Read the project's science rules and canon. Apply real science only after the story's rules are
   known. Canon may permit an impossibility; it may not silently rewrite ordinary physics.

## Claim census

Mark every science or technology claim before judging it:
- **O — ordinary:** real physics, biology, medicine, engineering; must be accurate.
- **E — extrapolation:** future but non-exceptional technology; needs mechanism, cost, limit and
  cross-chapter consistency.
- **A — allowed exception:** canon-designated impossible effect; test canon and narrative boundaries, not
  present-day feasibility.
- **M — metaphor/POV:** sensory or emotional language; flag only if readers will reasonably take it as
  literal mechanism.

Classes change sentence by sentence. One exceptional device never excuses unrelated errors nearby.

## Review order

1. **Continuity and exceptions:** technology owner, power source, user knowledge, and whether the effect
   is explicitly exceptional.
2. **Mechanics:** frame, gravity, acceleration, momentum, reaction mass, structural load, pressure, time.
   Orders of magnitude before simulation. → `references/orbital-spaceflight.md`
3. **Vacuum and thermal:** no air-path sound (structure-borne and radio are fine); vacuum removes
   convection, not radiation or conduction. Pressure force, venting, sublimation, heat balance, cycling.
4. **Orbits and travel:** orbit vs hovering vs rendezvous vs landing vs transfer; delta-v, burn
   direction, coast time, waste heat. FTL may be canon-exempt; local motion is not automatically exempt.
5. **Worlds and life:** gravity, atmosphere, climate, water phase, stellar activity, ecology,
   biosignatures and adaptation as one coupled system. → `references/planetary-astrobiology.md`
6. **Bodies:** anatomy, injury, exertion, healing time, exposure, radiation, sensory consequences. A
   safety guideline is not a hard human maximum.
7. **Cybernetics/prosthetics:** retained tissue, attachment, actuation, power, control signal, sensory
   return, fit, failure mode, maintenance, rehabilitation. → `references/cybernetics-prosthetics.md`
8. **Magic boundary:** extraordinary effects are legibly exceptional and obey established costs and
   limits. → `references/science-fantasy-boundary.md`
9. **Class sweep:** once one problem is found, search the whole scope for every instance.

## Diagnostic tests

- **Units first:** every quantity has a unit or unmistakable scale; convert once; compare to a known reference.
- **Conservation:** account for momentum, energy, mass, charge and heat. An exception must be named or
  unmistakably framed.
- **Pressure:** force = pressure difference × area. At 4% of Earth sea-level pressure each square metre
  carries about 4 kN; never "almost nothing" against a large hatch [4].
- **Thermal:** name heat sources and sinks and exposure geometry. "Space is cold" is not a mechanism;
  long-dead objects sit near radiative equilibrium unless cycling or internally heated [3].
- **Travel:** a position change in space needs a trajectory and momentum change, not distance/speed prose [1][2].
- **Habitability:** a habitable-zone orbit is a first filter; atmosphere, star, water, chemistry, geology
  and time still matter [8].
- **Biosignature:** one gas is evidence, not proof; ask for abiotic alternatives and context [10].
- **Resource ledgers:** when a change alters a counted resource (fuel, distance, money, travel time), build
  one ledger from the author's stated rules before any prose number is locked: every leg's cost with its
  arithmetic, where each threshold falls, and every line whose number changes. Check every stated rule at
  every waypoint, and the triangle inequality across all waypoints, since a cheaper detour through another
  stop can quietly reopen a route the author closed. Count reserves the same way in every feasibility
  check. Leave slack where the rules allow it, and make sure any figure the prose states can be worked out
  from figures the prose shows. When several ledger versions exist, name the one that governs and list the
  superseded ones.
- **Safety and redundancy:** for a safety-critical choice, compare the hazard the text shows, the hazard
  the characters are told about, and the failure the backup or test actually covers. A check for one
  failure mode (lost readouts) does not answer a different one (losing the operator's whole picture).
  Require the characters to disclose the functional risk; that does not require exposing withheld lore.
- **Load:** compare carried mass, grip, posture, distance, injury, fatigue and gravity [14]. Flag literal
  tissue damage if later performance ignores it.
- **Interface:** separate motor command, proprioception, touch, pain and vision. "Neural link" does not
  grant every channel by default [11][12][13].
- **Machines:** robot or AI behaviour (intent, pursuit, retreat, hesitation) follows from orders,
  programming or control logic unless canon grants another mechanism. Characterful behaviour does not
  imply a biological drive (fear, instinct, hunger) or biological anatomy; flag lines that explain
  machine action through one without canon support.
- **Exception (Sanderson's laws as craft diagnostics):** if magic solves a conflict, readers must already
  understand the capability; limits matter more than powers [18][19].

## Severity and fix discipline

- **Critical:** breaks a protected canon rule, the plot's causal chain, a survival outcome or a
  foundational science premise. Escalate; never improvise canon.
- **Major:** conspicuous failure needing a mechanism, scene-level rewrite or several linked corrections.
- **Minor:** wording, unit, scale or local plausibility, fixable in a line or two.

For each problem give `file:line`, class (O/E/A/M), severity, why it fails, the smallest fix, and all
other instances. Preserve the beat: prefer changing one noun, number, sensory channel or sentence over
adding exposition. If the smallest accurate fix would expose withheld lore, report it and ask for a
canon decision instead of inventing exposition.

## Verification checklist

- [ ] Revision recorded; full scope read.
- [ ] Binding constraints and canon checked.
- [ ] Every claim assigned O/E/A/M.
- [ ] Numbers, units and orders of magnitude checked.
- [ ] All instances of each problem class listed.
- [ ] No finding exposes withheld lore or contradicts a no-op.
- [ ] Smallest fix preserves POV, pacing and the story beat.
- [ ] Non-obvious real-world claims cite primary or authoritative sources; never invent a citation.

## Project specifics

The project's own skill supplies: the science rules file and canon, which effects are sanctioned
exceptions and their limits, the devices and bodies worth tracking (with their canon terms), the
chapter map of science-heavy passages, and withheld lore that fixes must not explain.
