---
name: docs-drift-sweep
description: Use when keeping project docs in step with merged code, or reviewing a docs PR.
metadata:
  version: "0.1.2"
  status: draft
  owner: maintainer
---

# Docs drift sweep

A recurring pass that finds where merged code made the docs wrong, incomplete or hard to read, and
fixes all of it in one docs PR. The same verification traps apply when *reviewing* a docs PR.

## When to use

- A scheduled docs-steward run, or a request to bring a docs site up to date.
- Reviewing any PR that is mostly prose: docs pages, indexes, status lines, instruction files.

## Principles

- **Start from what changed, not from the docs.** The diff since the last sweep is the work list;
  reading the whole site every day burns tokens and misses nothing the diff would not show.
- **Every factual sentence is a claim about code or live state.** Verify it against the code at the
  integration tip or a live probe, not against another page.
- **Readability is part of correct.** Answer first, short sections, plain words, tables where they
  help, links between sections, no walls of text.
- **Never delete or descope on your own.** Content that looks obsolete, conflicting owner
  statements, removing an initiative: list them for the owner.

## Procedure

1. **Range.** Start from the commit range recorded by the previous sweep; if none, the last day.
   For every merged change, find the pages and status/index lines it affects. A maintained map from
   code paths to docs pages (if the project has one) is faster than guessing.
2. **Check affected pages against the code**: wrong facts, missing pages for new features, dead
   links and anchors, duplicates, pages misplaced in the navigation. Use the project's docs search
   before raw grep.
3. **Check readability** against the principles above.
4. **Nothing found** → one line `No docs drift in <from>..<to>` and stop.
5. **Fix everything in one PR** from your own worktree off the integration branch. Regenerate any
   generated index the project keeps. Run the docs checks and record each command and result.
   Take the list of checks from the repo at the commit you branched from: the root and nearest
   instruction files (`AGENTS.md` or equivalent), the contributing or authoring docs, and the package
   scripts (for example `package.json`). Do not use a list kept in a skill or in memory: repo checks
   are added and renamed more often than skills are updated. Record that commit in the PR body.
6. **Hand off for review** with the commit range checked, the PR link, and an "Owner decisions"
   section for anything you would not decide alone.
7. End with one line: `Docs PR #<N> for <from>..<to>: <k> pages fixed` (plus owner decisions, if
   any).

## Pitfalls

The traps that let wrong docs pass every check are in
[references/docs-verification-traps.md](references/docs-verification-traps.md): checkers in
changed-files mode, anchor checkers that ignore plain links, formatters that silently move
emphasis, documented commands nobody ran, dated status notes that went stale, CI descriptions that
don't match the job's `if:`.

- **Drift you may not edit is still a finding.** If a write guard refuses a file the sweep needs to
  correct (protected agent-instruction files, for example), do not route around it with another
  tool. Ship the editable fixes. Report the location and redacted summary in the PR; send the exact
  correction only through an authorized channel to someone who may edit it. Do not report it as fixed.

## Verification

The PR body lists the commit range, every page touched, every check run with its result (and the commit the check list was read from), and the
owner decisions. Each corrected fact cites the code location or probe that proves it.

## Project specifics

The project's docs-sweep skill or `AGENTS.md` supplies the docs root, the drift map, the index
generator, the check commands, the docs search tool, the worktree path, the reviewer to hand off to,
and who merges.
