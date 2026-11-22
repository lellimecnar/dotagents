# Public-diff audit evidence

Use when the audit reviews proposed changes to a public corpus and its findings are delivered as exact edits that
someone (or something) will apply mechanically. The value protected: every quoted span and replacement applies
cleanly and lands where the finding says. For a prose-only review of a whole tree, the main workflow is enough.

- Index the additions by change (PR or patch), target file and hunk before reviewing. Validate each proposed quote
  against a contiguous run of added lines: it occurs exactly once in the complete post-change file and obeys
  any requested quote limit. Checking additions alone does not detect an identical unchanged span.
- Distinguish hardcoded local requirements from explicitly optional or jurisdiction-scoped public examples.
  Generalize the requirement without stripping useful public citations, and without implying that a named
  diagnosis, belief or circumstance is the publisher's own.
- When both a review tree and a diff are supplied for newly added files, compare the full file inventory and
  contents against the diff. Dry-run the exact edits in memory: reject overlapping match spans, and check that the
  replacements still read correctly together in their surrounding text.
- If only a patch is supplied, verify each quote against the literal post-change hunk lines (additions, plus
  context when the caller permits it) with diff markers removed. Never quote a deletion or a hunk header. If a
  separately supplied snapshot matches the patch's base blob ID, apply the hunk in memory and verify the result
  against its new blob ID before checking full-file uniqueness. Otherwise keep hunk-level matches apart from
  full-file uniqueness, and do not claim anchor resolution or an applied-edit test.
- On a follow-up pass, test whether each prior quote is gone and the proposed replacement is present, then assess
  the result and neighbouring rules on their own. Compare the old and new reports over the same scope, so a moved
  finding does not look fixed. Literal application alone does not show that a correction works in context.
- A denied or unavailable span is unverified. Never mark a partially inspected change clean; list what you could
  not open.
