# Verifying animations

An animation's defects live mid-flight: speed mismatches, clipped elements, stalls, two parts
drifting apart. Start and end screenshots cannot show them.

1. **Use supported playback controls.** Slow, pause, or step through motion when useful.
   Reset using a supported test path. If playback cannot be controlled, inspect a recording
   frame by frame or request a narrowly scoped test hook.
2. **Remove what hides the bug, temporarily.** Where authorized, isolate the visual effect
   with a test-only override that preserves the motion being measured. Mark every such edit (for
   example a `TEMP DEBUG` comment) and search for the marker before anything is committed.
3. **Capture frames mid-flight**, several per transition (a screen recording or repeated
   screenshots), not just before and after. Keep the recording as review evidence.
4. **Sample, don't eyeball.** When supported, sample the relevant
   positions or other properties through authorized test tools. Otherwise measure from a recording.
   Choose a sample interval suited to the transition and record the metrics and interval used. Divergence and velocity bumps become numbers.
5. **Check coordination against the intended motion.** Compare linked elements’ timing and
   paths with the specification; require lockstep only where the design calls for it.
6. **Check reduced motion too**: with the user's reduced-motion preference set, the transition is
   removed or simplified as the spec says, and the end state is the same.

**Acceptance:** Compare the observed path, timing, and end state with the intended behavior.
Record the test conditions and measurements. Attach only evidence authorized for the audience;
use synthetic data or redact unrelated private information.
