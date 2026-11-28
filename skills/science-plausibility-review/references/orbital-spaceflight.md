# Orbital mechanics, spaceflight, vacuum, and loads

Use this as a diagnostic sheet, not as on-page exposition. Source labels resolve in `sources.md`.

## Core models

- An orbit is continuous free fall around a body; a ship does not need continuous thrust merely to remain in a stable orbit [1].
- Chemical missions commonly apply short burns then coast; low-thrust electric propulsion can thrust gently for months or years [2].
- Momentum changes require impulse. If debris, escaping gas, a tow line, or an engine changes one object's motion, track the equal-and-opposite exchange unless an explicit external field supplies it [1][2].
- A Hohmann transfer changes orbital energy with timed burns; rendezvous also requires matching position and velocity. “Point and accelerate” is inadequate orbital prose unless the drive is an explicit high-thrust abstraction [2].

## Review worksheet

| Claim | Ask | Minimum adequate cue |
|---|---|---|
| “In orbit” | Around what, at what altitude/period? | body + stable/free-fall context |
| “Stopped/hovering” | Relative to what frame? | station-keeping, surface, or target frame |
| Course change | Where does impulse come from? | engine, venting gas, collision, field |
| Rapid turn | What happens to crew/cargo? | restraints, inertial compensation, or injury |
| Long burn | Where does waste heat go? | radiator, thermal store, consumable sink |
| Atmosphere entry | How are speed and heat managed? | heat shield/lift/drag/propulsion |

## Vacuum and thermal rules

- Vacuum has no air-path sound. Radio or structure-borne vibration is valid if the receiving character has a connected path or sensor.
- In vacuum, exterior heat transfer is radiation plus conduction through contact; there is no convection. Temperature follows absorbed sunlight/planetshine/internal power versus emitted radiation [3].
- “Space is near absolute zero” is not a sufficient object temperature. Surface properties, orientation, star distance, shadows, and internal heat set equilibrium [3][5].
- Exposed volatile ice can sublime; centuries-old frost needs deep cold, shadow, replenishment, or another preservation mechanism [5].
- Thermal contraction happens during a temperature change. A centuries-old object needs rotation, eclipse, changing illumination, or active cooling to keep contracting/ticking now [3].

## Pressure and hatches

Pressure force: `F = ΔP × area`.

Worked example: 4% of 101,325 Pa is about 4,053 Pa. That gives about 4,053 N on each square metre, or 8,106 N on a 2 m² hatch. NASA hatch design treats pressure equalization and seal interstitial volumes as operational load issues [4].

Review sequence:
1. Establish pressure on each side.
2. Estimate hatch/door area.
3. Ask whether latches, hinges, debris, and people can take the force.
4. Vent through a controlled path before cutting.
5. Track gas impulse, loose debris, cooling, condensation, and visibility.

## Loads, gravity, and bodies

- Mass remains relevant in microgravity: it resists acceleration and carries momentum.
- In shared free fall, do not assume a gravity-defined bottom: loose objects do not fall toward
  the deck, and fluids do not settle or stratify by buoyancy as on Earth. Sweep for words such as
  *sank*, *settled*, *pooled*, *poured*, *layered*, and *the low end*. Check what drives the effect:
  wetting and capillarity can collect liquid or separate phases, and thrust or spin can supply an
  effective down direction. Vacuum alone says nothing about weightlessness. If nothing in the scene
  supports the effect, revise the unsupported image before adding machinery or a procedure.
- For each maneuver, estimate propellant from required delta-v, starting mass, and engine specific
  impulse. A longer coast does not by itself spend main-engine propellant. Different burn rates on
  different legs are not necessarily an error: check thrust, burn duration, and any material gravity,
  drag, or steering losses.
- Weight changes with local gravity; structural and muscular forces also depend on posture, acceleration, and leverage.
- NASA/NIOSH's lifting equation is a risk assessment using mass, hand position, travel, asymmetry, frequency, duration, and grip—not a universal maximum [14].
- Deep-space radiation can cause cancer, tissue, CNS, and acute-syndrome risks; exposure depends on duration, shielding, solar events, and mission environment [15].

## Common small fixes

- Change “heard” to “felt through the boots/suit frame” in vacuum.
- Add “as the wreck rotated into shadow” to justify ongoing contraction.
- Change an unsafe pressure number, or keep it and show complete venting before breach.
- Replace exact but unsupported travel time with a relative cue unless the trajectory is available.
- Add one restraint, tether, radiator, shield, or recovery consequence rather than a lecture.

## Sources

[1] https://science.nasa.gov/learn/basics-of-space-flight/chapter3-4
[2] https://science.nasa.gov/learn/basics-of-space-flight/chapter4-1
[3] https://www.nasa.gov/smallsat-institute/sst-soa/thermal-control
[4] https://www.nasa.gov/wp-content/uploads/2023/12/ochmo-tb-028-vehicle-hatches.pdf
[5] https://nasa.gov/wp-content/uploads/2021/03/544886main_ls5_cool_space_suits_c1_final3.pdf
[14] https://cdc.gov/niosh/ergonomics/about/RNLE.html
[15] https://www.nasa.gov/directorates/esdmd/hhp/space-radiation
