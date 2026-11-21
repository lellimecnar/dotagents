# Reader-knowledge checks with a position-bounded reader

A reader-position tool answers from a built e-book only up to a saved reading position. That makes it
useful for checking what a reader could retrieve by a chapter boundary. It does not decide canon, and an
empty answer does not prove a passage is absent. Sources `[S#]` resolve in [sources.md](sources.md).

## Setup

1. Build or rebuild the e-book from the exact manuscript revision under review (see `manuscript-book-build`).
   Never let your own working branch change underfoot: build from a detached, throwaway checkout.
2. Record the source revision next to the artifact (e.g. a `<book>.epub.commit` file) and report it.
3. Open the book in the tool and inspect the returned spine. Front matter (title page, contents) shifts
   spine positions relative to book chapters; the offset depends on the build. **Never assume the mapping
   after a rebuild.**
4. Set progress at the intended *finished chapter* and confirm the returned chapter title matches.
5. Re-open the book after every rebuild so the tool re-indexes.

## Query design

Ask for retrievable evidence, not a verdict.

Good: two distinctive exact phrases (`"left on the altar" "under the ash"`); one entity/action per
call; "What exact passage first lets the reader infer X?"

Weak: "Does chapter 5 have a continuity error?"; "What is canon about the relic?"; "Did the author intend
dramatic irony?"

For noisy results:

1. Replace concepts with two distinctive exact phrases.
2. Search one entity/action per call.
3. Ask before and after the suspected reveal boundary.
4. Read the chapter manually if retrieval stays empty or irrelevant.
5. Record `the tool did not retrieve evidence`; never record `the book does not say` from an empty result.

## Reader-knowledge protocol

| Checkpoint | Question | Record |
|---|---|---|
| Before installation | Can the reader retrieve the fact or only a seed? | exact passage / none retrieved |
| At installation | What new evidence appears? | quote, chapter, confidence |
| Before payoff | What belief should the reader hold? | known / suspected / ambiguous |
| At payoff | Is the answer earned and consistent? | quote and earlier seed |

Then compare with each character's acquisition. Dramatic irony requires the reader's evidence to exceed
the character's; mystery often reverses that or withholds truth from both. Do not label either from
truth alone [S8–S9]. When a reader-position answer and canon disagree, that is itself a finding: a seed is
missing, or a fact is revealed too early.

## Channel checklist

Tag every acquired fact by channel: direct sight/sound/touch; device overlay or synthesized speech;
translated utterance; telepathic or other non-sensory contact; implant or link synchronization; network
signal/image; memory or dream; inference from tracks, residue or behaviour; deliberate disclosure, lie,
silence or mistranslation. Similar sensory wording does not make two channels identical. Record who had
access, what the signal contained, and whether the recipient treated it as reliable.

## Evidence note template

```markdown
- Artifact source revision: `<sha>`
- Artifact checksum: `<sha256>`
- Build checked: `<YYYY-MM-DD>`
- Saved position: spine N / book Chapter N (`<returned title>`)
- Query: `<exact query>`
- Retrieved: `<quote>` (`<tool chapter title>`)
- Manual source: `<chapter file>:line-line`
- Supports: reader knew | suspected | did not yet have retrievable evidence
- Limits: retrieval is bounded evidence, not canon and not exhaustive
```

Spine positions are build outputs, not durable facts: a rebuild from the same revision can shift
them. Record the observed mapping, checksum and check date in the task evidence, never in a skill.
