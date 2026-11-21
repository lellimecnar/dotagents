# Public-diff audit evidence

Use when the audit reviews proposed changes to a public corpus and its findings are delivered as exact edits that
someone (or something) will apply mechanically. The value protected: every quoted span and replacement applies
cleanly and lands where the finding says. For a prose-only review of a whole tree, the main workflow is enough.

- Index the additions by change (PR or patch), target file and hunk before reviewing. Validate each proposed quote
  against a contiguous run of added lines: it occurs exactly once in that file's additions and fits the requested
  quote limit. Otherwise old context, removed lines and ambiguous strings end up in the edit inventory.
- Distinguish hardcoded local requirements from explicitly optional or jurisdiction-scoped public examples.
  Generalize the requirement without stripping useful public citations, and without implying that a named
  diagnosis, belief or circumstance is the publisher's own.
- When both a review tree and a diff are supplied for newly added files, compare the full file inventory and
  contents against the diff. Dry-run the exact edits in memory: reject overlapping match spans, and check that the
  replacements still read correctly together in their surrounding text.
- A denied or unavailable span is unverified. Never mark a partially inspected change clean; list what you could
  not open.
