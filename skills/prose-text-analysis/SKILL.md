---
name: prose-text-analysis
description: Use when measuring prose: readability, n-grams, overuse, avoid-list hits.
metadata:
  version: "0.1.1"
  status: draft
  owner: maintainer
---

# Prose text analysis

A dependency-free analyzer (`scripts/analyze.py`, Python standard library only) that reports word,
sentence and paragraph statistics, vocabulary measures, six readability formulas, top 1–4-grams,
overuse alerts, and hits from a project's severity-graded avoid-list with line numbers and context. Use
it to **locate** problems for a human or agent reader to judge. A count is never a finding by itself.

## When to use

- Mechanical pass after drafting or revising a chapter.
- Corroborating an editorial, pacing or readiness assessment (always after the cold read).
- Tracking quotas (e.g. "nodded ≤ 3 per chapter") and repeated phrases across chapters.

## Usage

```bash
python3 scripts/analyze.py <file> [more files or globs]   # e.g. chapters/*.md
python3 scripts/analyze.py --text "Sample text to analyze"
python3 scripts/analyze.py <file> --json | jq '.avoid_list'
python3 scripts/analyze.py <files> --markdown
# options: --top N (20)  --min-freq N (2)  --no-stopwords  --json  --markdown
python3 scripts/test_analyze.py        # regression tests
```

## The avoid-list

The script looks for `guides/avoid-list.md` (legacy: `.github/resources/avoid-list.md`) in the repo holding
the first analyzed file, walking up its parents, then in the repo holding the current directory. It never
searches from its own location, so a shared copy works for any project. If none is found it warns on
stderr and **skips avoid-list checks**; a report with no avoid-list section means "not checked", not
"clean". Run from the project worktree when analyzing a file stored elsewhere.

Format: level-2 sections whose headings contain the severity marker, each with a markdown table whose
first column is the phrase:

| Section heading | Meaning | Action |
|---|---|---|
| `## 🚫 BANNED` | never use | delete and rewrite |
| `## ⛔ FORBIDDEN` | effective prohibition | replace every time |
| `## 🔴 AVOID` | strongly discouraged | rewrite unless justified |
| `## 🟠 LIMIT` | fine in moderation | track frequency against a quota |
| `## 🟡 CAUTION` | acceptable if purposeful | watch clustering |

Typical content: clichés, AI-tell words, filter verbs, hedges, eye/face/body clichés, overused physical
beats, dialogue-tag issues, opening clichés. The project owns the list and its quotas.

## Reading the numbers

- **Readability:** Flesch Reading Ease (0–100, higher is easier), Flesch-Kincaid Grade, Gunning Fog, SMOG,
  Coleman-Liau, ARI. FRE bands: 90–100 very easy (5th grade); 80–89 easy (6th); 70–79 fairly easy (7th);
  60–69 standard (8th–9th); 50–59 fairly difficult (10th–12th); 30–49 difficult (college); 0–29 very
  difficult. A common YA target is FRE 60–80, FK 5–9, Fog 8–12, but formulas measure word and sentence
  length, not comprehension; use them to find dense outliers, never as pass/fail.
- **What moves the score:** Flesch Reading Ease and Flesch–Kincaid Grade Level use average words
  per sentence and syllables per word. Compare how much each input actually changes; their coefficients
  are in different units. If sentence lengths already suit the scene, inspect diction for dense
  outliers. Keep words the character would use, skip decorative thesaurus swaps, and tune density
  while drafting each unit rather than micro-editing an assembled chapter to hit a score.
- **Overuse thresholds (script defaults):** a word above 1% of total words; 2-grams above 5 per 1,000
  words; 3-grams above 3 per 1,000; 4-grams above 2 per 1,000.
- **Vocabulary:** unique words, type-token ratio, lexical density. Compare chapters of similar length only;
  TTR falls as length grows.

## Method

1. Read the passage first. Form your own judgment.
2. Run the analyzer on the exact revision under review.
3. Inspect **every** flagged instance in context. Promote one to a finding only when the repetition or
   phrase changes rhythm, clarity, voice or emotional force, or breaches a stated quota.
4. Group findings by class and list every instance; cite `file:line`.
5. Report the figures with their method ("raw Markdown, whitespace-split").

## Pitfalls

- Missing avoid-list → silent-looking skip. Check stderr or the presence of the section.
- Markdown syntax, headings and scene-break glyphs affect counts; say what was counted.
- Character names and invented terms dominate n-grams; that is expected, not overuse.
- Dialogue tags like "said" are meant to be invisible; frequency alone is not a fault.

## Project specifics

The project's own skill supplies: the avoid-list file and its quotas, readability targets for its
audience, and where the analyzer is installed for its agents.
