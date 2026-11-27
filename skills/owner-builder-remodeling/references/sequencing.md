# Sequencing a basement finish: phases, hold points, dependencies

Source IDs refer to `sources.md`. "HOLD" = do not proceed until it passes/clears.

| # | Phase | Depends on | Hold point / output | Key source |
|---|---|---|---|---|
| 0 | Authority check: jurisdiction, code edition, permit status | - | Written answers from the building dept | - |
| 1 | Inventory existing work vs approved plans and inspection record | 0 | Inventory sheet + photos | - |
| 2 | Water problems fixed at source | 1 | HOLD: dry for a season of observation if there was a leak (judgment) | [S33][S40] |
| 3 | Radon test of the lowest level | - (start early; runs in parallel) | Result; if >= 4 pCi/L plan mitigation before walls close | [S30][S32] |
| 4 | Design decisions: bedroom or not (egress), ceiling heights, mechanical room size/combustion air, bath location vs existing rough-in, alarm plan | 1-3 | Scaled floor plan | [S15][S23][S24] |
| 5 | Scope + written bids for trade/pro items (engineer, egress, mitigation, any hired trade) | 4 | Bid comparison; household decision | [S50] |
| 6 | Permit application + owner affidavit; fees decided by the household | 4-5 | Permit issued; expiry date calendared | [S18] |
| 7 | Egress cut/well, radon mitigation rough (if any), structural changes | 6 | Their own inspections per ticket | - |
| 8 | Layout, PT bottom plates, perimeter foam on concrete and rim joist, framing, soffits | 6 (and 7 if walls interact) | Fireblocking installed as framing goes up | [S15][S38] |
| 9 | Rough plumbing (drain/vent/supply, test), rough mechanical (ducts, gas), rough electrical (boxes, home runs, alarm wiring/interconnect), low-voltage | 8 | HOLD: rough inspections (and rough framing) pass | - |
| 10 | Cavity insulation, air sealing, remaining fireblocking | 9 passed | HOLD: insulation inspection passes | - |
| 11 | Drywall (non-paper-faced below grade, 1/2 in off slab), tape, finish | 10 passed | Any drywall/fastener inspection if on the ticket | [S39] |
| 12 | Paint (permeable), doors, trim, flooring | 11 | - | [S37] |
| 13 | Trim-out: devices, fixtures, smoke/CO alarms (interconnected), registers, plumbing fixtures | 12 | Functional test (GFCI/AFCI, alarms) | [S15][S48] |
| 14 | Final inspection(s) | 13 | Permit finaled in portal | - |
| 15 | Radon retest; file closeout records | 14 | Records folder | [S32] |

## Dependency rules
- Nothing gets covered before its inspection passes (IRC R109 model inspection sequence; the permit ticket governs).
- The next inspection is the real milestone; each phase ends by scheduling it.
- Permit life (IRC model R105.5): invalid if work does not commence within 180 days of issuance or is
  suspended/abandoned for 180 days; written extensions up to 180 days each [S18]. The model rule does
  not make inspections the operative event, but local AHJs may use inspections as evidence of active
  work or set their own expiration/reactivation procedure. Confirm the local rule in writing, and
  request inspections promptly so activity has a dated record.
- Order inside step 9: plumbing drains first (least flexible, gravity), then ducts, then gas, then
  electrical, then low-voltage (most flexible). (Trade practice; judgment.)
- Buy materials per phase, not for the whole job, so a stall does not leave drywall and lumber
  absorbing moisture or getting lost. (Judgment.)

## Cutting phases into startable blocks
A block: one session, one concrete result, staged kit, safe stopping state, ends with a reset.
Example breakdown of phase 8 (illustration):
1. Snap chalk lines for all perimeter walls (kit: chalk line, tape, pencil, plan sheet).
2. Cut and glue/seal foam board on one wall (kit: foam, adhesive, sealant, knife, straightedge).
3. Seal rim joist bays on one side (kit: foam board offcuts or spray foam, PPE).
4. Cut and fasten PT bottom plates on one wall (kit: PT lumber, powder-actuated or concrete fasteners, PPE).
5. Frame one wall and install its top fireblocking.
Each block ends with: tools back to their marked home, offcuts in the bin, photo of progress.
