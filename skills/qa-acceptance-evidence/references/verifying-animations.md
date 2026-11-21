# Verifying animations

An animation's defects live mid-flight: speed mismatches, clipped elements, stalls, two parts
drifting apart. Start and end screenshots cannot show them.

1. **Route all motion through one time-scale knob** (a CSS custom property or config value that
   multiplies every duration). Then any transition can be slowed 10-60x for inspection, and a
   near-zero value snaps it to completion to reset state between takes. If the product has no such
   knob, ask for one as a testability fix rather than inspecting at full speed.
2. **Remove what hides the bug, temporarily.** Pin crossfading elements to full opacity with
   transitions off so both are visible; position bugs hide under fades. Mark every such edit (for
   example a `TEMP DEBUG` comment) and search for the marker before anything is committed.
3. **Capture frames mid-flight**, several per transition (a screen recording or repeated
   screenshots), not just before and after. Keep the recording as review evidence.
4. **Sample, don't eyeball.** Drive the transition from a script and sample each moving element's
   bounding box and computed opacity every few hundred milliseconds into a table (position,
   opacity, delta per sample). Divergence and velocity bumps become numbers.
5. **Move linked elements in lockstep**: same distance, duration and easing from one source.
   Separately tuned timing functions drift.
6. **Check reduced motion too**: with the user's reduced-motion preference set, the transition is
   removed or simplified as the spec says, and the end state is the same.

**Acceptance:** the slowed run and the sample table show monotonic, matched motion with no clipping
or stall; record the time scale used, and attach the recording and the table to the verdict.
