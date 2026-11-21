---
name: owner-builder-remodeling
description: Use when planning, permitting or restarting a home remodel.
metadata:
  version: "0.1.2"
  status: draft
  owner: maintainer
---

# Owner-builder remodeling (basement finishing as the worked case)

The method a residential general contractor uses to scope, permit, sequence, inspect and close a
basement finish or similar interior remodel, written for an owner-builder household. Numbers,
thresholds and citations live in `references/`; every figure there has a source URL. Code text changes
by edition and local amendment, so treat the references as "what to check", and treat the building
department's written answer as the authority.

## When to Use
Any task about a residential remodel or basement finish: reading plans/permit records, current-state
assessments, permit restarts, code questions (egress, alarms, fireblocking, insulation, radon),
sequencing work into blocks, scoping and comparing bids, or deciding when to call in a licensed pro.
Use `grounded-citations` when the output is a cited report.

Reference files:
- `references/sources.md` - numbered source list, plus where sources disagree and which one wins.
- `references/codes-and-thresholds.md` - egress, alarms, fireblocking, ceiling height, insulation R-values, combustion air, GFCI/AFCI, radon.
- `references/permits-and-inspections.md` - who may do what, jurisdiction, applying, inspections, permit expiry, restarting with a new permit.
- `references/building-science-basements.md` - moisture, insulation assemblies, radon in a finish.
- `references/sequencing.md` - phase order, hold points, dependencies.
- `references/bids-estimating-restart.md` - scoping, written bids, contracts, liens, restarting a stalled job, designing around lost tools.

## Method (follow in order)

### 1. Establish the authority before anything else
- Confirm the jurisdiction from the address (city vs county; a city mailing address is not proof).
- Find the adopted code editions and local amendments and their effective date. A new permit is
  reviewed under the code in force when it is filed, not the one the old plans were drawn to.
- Find the permit's real status in the online portal: issued, expired, finaled, which inspections passed.
- Decision rule: if the status, the code edition or the scope is unclear, the next action is a
  written question to the building department (email or portal), not work on site.

### 2. Inventory what exists before planning what's next
Walk the space with the approved plans and inspection record. For each system (framing, plumbing,
electrical, mechanical/gas, insulation, fireblocking, egress, alarms) record: installed? inspected and
passed? still visible? damaged or changed since? Photograph every open wall before it closes.
Use the restart checklist in `references/bids-estimating-restart.md`.

### 3. Clear the life-safety and health gates first
These can change the design, so settle them before framing or buying finish materials:
- **Water**: any active leak, efflorescence, wet slab or bulk water must be fixed at the source before
  insulation or drywall. Moisture-sensitive materials do not touch concrete.
- **Radon**: test the lowest level before finishing; it is cheaper to mitigate before walls close.
  Retest after the work.
- **Egress**: by default (2024 IRC R319.1) basements and every sleeping room need an emergency escape
  and rescue opening, subject to the R319.1 exceptions (e.g. the sprinklered-dwelling exception for
  basement sleeping rooms) and local amendments; the minimum opening size depends on whether it is a
  grade-floor opening (see `references/codes-and-thresholds.md`). A new bedroom with no compliant window
  means a foundation cut, which is engineering + permit + usually a licensed contractor. Decide
  bedroom-or-not early.
- **Ceiling height** at beams/ducts, and **combustion air** if a furnace/water heater room will be
  enclosed. Both can force a redesign.
- **Alarms** (two separate triggers; verify against the locally adopted code and amendments):
  - *Smoke* (2024 IRC R310.2.2 model text): permitted alterations, repairs or additions require smoke
    alarms located as for new dwellings, except work on exterior surfaces (roofing, siding, windows,
    doors, porch/deck) and plumbing or mechanical work. A basement finish is normally caught.
  - *CO* (2024 IRC R311.2.2 model text): permitted alterations, repairs or additions require CO alarms
    as for new dwellings, except exterior-surface work, plumbing, and mechanical work that is not
    fuel-fired; the CO rule applies only where there is a fuel-fired appliance or an attached garage.
    Some states also have a CO-alarm statute triggered when permitted interior work occurs or a
    sleeping room is added, often with its own placement rule (e.g. within a set distance of each
    sleeping-room entrance); check your state statute and follow whichever is stricter.

### 4. Scope, then price, in writing
- Write the scope as a room-by-room, system-by-system list tied to the plan sheet. No scope, no bid.
- Separate owner-labor items from trade items. Default: structural changes, foundation cuts and
  anything needing a stamped design go to licensed pros; electrical/plumbing are owner-doable only
  where the state and local homeowner exemption applies and the owner will personally do the work.
- Get at least three written bids on the same written scope; compare line by line, not by total.
- Every price goes to the household's decision-maker as a short cost/benefit with the cheaper
  alternative. Prepare quotes and carts; never sign, submit or pay.

### 5. Permit
- Assemble the submittal from the jurisdiction's checklist (floor plan to scale, room uses, egress,
  alarm locations, electrical/plumbing/mechanical layout, insulation values, structural changes).
- Owner-builders file the owner affidavit where required. Hired trades must be licensed and on the
  permit before it issues.
- Calendar the expiry rule the day the permit issues. The IRC model rule (R105.5): the permit becomes
  invalid if authorized work does not commence within 180 days of issuance, or is suspended or
  abandoned for 180 days after it starts; the building official may grant written extensions (up to
  180 days each) requested in writing before expiry. The model text does not make an inspection the
  operative event. Local AHJs may treat inspections as the evidence that work is active, or impose
  their own expiration/reactivation procedure, so get the local rule confirmed in writing and
  schedule inspections promptly so there is a dated record.

### 6. Sequence into startable blocks
Order: water/radon fixes -> layout and bottom plates -> framing + fireblocking -> rough plumbing,
mechanical, electrical, low-voltage -> rough inspections -> insulation + air sealing -> insulation
inspection -> drywall -> finishes -> trim/devices/alarms -> final. Never cover work before its inspection
passes. Then cut each phase into blocks that:
- fit one work session,
- start with a named first physical action ("snap the chalk line for the north wall"),
- end at a state that is safe to leave for weeks,
- have every tool and material staged before the block starts.
Aim for the next inspection as the milestone that matters; schedule it the moment a phase is ready.

### 7. Design around lost tools and scattered materials
Treat it as a site-logistics problem. One home for tools (shadow board or labelled bins), one staging
zone per phase, a per-block kit list, material delivered close to when it is used, and an end-of-block
reset that is part of the block. Don't propose tidying as a substitute for permits or progress.

### 8. Close out
Pass final, get the permit marked finaled in the portal, keep the approved plans, inspection record,
radon retest, warranties, lien waivers and photos together. Retest radon after the finish.

## Stop and refer (do not advise around these)
- **Emergency**: gas smell, CO alarm sounding, sparking/burning smell, flooding near electrical:
  leave and contact local emergency services or the applicable utility emergency line from a safe location.
- **Health (stop construction advice; you do not diagnose):** headache, dizziness, confusion or nausea
  after a possible carbon-monoxide exposure -> fresh air and emergency medical help now. Possible lead
  exposure from disturbing old painted finishes, even with no symptoms -> stop that work and suggest asking
  a health-care provider about a blood lead test. Mold-related health concerns (asthma, allergy) -> a health
  professional before anyone does cleanup. Then return only to the construction side (find and fix the
  source, test, refer the trade).
- **Structural**: removing or altering posts, beams, joists, load-bearing walls, or cutting the
  foundation (egress) -> licensed structural engineer, and usually a licensed contractor.
- **Gas piping and appliances, panel or service changes** -> licensed trade unless the homeowner
  exemption clearly applies and the owner is competent; the inspector is the backstop, not you.
- **Active water intrusion, foundation cracks that move or leak, heavy mold** -> a
  waterproofing/foundation professional before any finish work.
- **Radon at or above the action level** -> a licensed/certified mitigator (or a carefully planned
  owner install where the state allows), then retest.
- **Suspected asbestos or lead** in old finishes being demolished -> testing before disturbance.
- Legal disputes (liens, contract breach) -> attorney; you only organise the paperwork.

## Common mistakes to catch
- Covering work before the rough or insulation inspection passed.
- Letting a permit lapse: no work started within 180 days, or a 180-day stall (model rule), or
  missing the local rule (some AHJs judge activity by inspections). Confirm the local rule in writing.
- Assuming a new permit can reuse old plans; edition changes can add requirements.
- Polyethylene or vinyl on the interior of a basement wall; fiberglass or paper-faced drywall
  against concrete; untreated plates on the slab.
- Missing fireblocking at the top of furred walls, soffits and every 10 ft along the wall.
- Enclosing the furnace/water heater in a room too small for combustion air.
- Calling a room a bedroom without a compliant egress opening.
- Alarms not interconnected; CO alarms forgotten outside new sleeping rooms.
- Comparing bids with different scopes; paying large deposits; no lien waivers.
- Buying all materials up front for a job that will stall; they get damaged, lost or obsolete.

## Output rules
Give few options, already prioritised, with the first physical action named, each compared against
the alternatives before one is called best (`due-diligence-before-deciding`). Report what you
verified, then a short Limits section: only what no available source (code text, the building
department, the permit portal, the manufacturer) could settle after you tried, what you tried, and
why the recommendation does not depend on it. An unverified fact never carries a recommendation.
If a deciding fact is unverified, pause the recommendation and seek written clarification from the
local authority or other source responsible for that fact. Prefer written channels (email, web form, portal) for
anything sent to a building department or contractor, so there is a record.
