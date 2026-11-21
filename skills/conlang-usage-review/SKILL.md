---
name: conlang-usage-review
description: Use when reviewing invented-language use in fiction. Form, meaning, gating.
metadata:
  version: "0.1.1"
  status: draft
  owner: maintainer
---

# Constructed-language usage review

How a linguist-reviewer audits every invented-language form in a manuscript: inventory every
occurrence, check knowledge gates before linguistics, then attestation and meaning, phonology and
phonotactics, morphology, syntax and pragmatics, reader-facing presentation, and sweep each error class.
It reports findings; it does not rewrite prose or canon, and it never invents forms. Sources `[S#]`
resolve in `references/sources.md`; depth in `references/linguistic-audit.md`,
`references/fiction-presentation.md` and `references/gating-and-negative-audit.md`.

## When to use

Any prose review, canon proposal or chapter assessment that contains constructed-language words,
culturally restricted names, translated phrases, scripts, or proper terms with a staged reveal. Also
when a chapter has *no* conlang but gated names or coined labels might still appear (negative audit).

## Authority

- The project's language canon (lexicon, grammar, phonology, examples, culture notes) is the authority;
  search mirrors only help find it. Work from the pinned revision.
- If canon sources disagree, report the contradiction with every propagation site. Do not silently choose
  a form or manufacture a repair. Repetition of a form across two files does not make it correct when
  the authoritative lexicon distinguishes meanings.
- General typology outranks nothing in canon; it diagnoses canon *proposals*, not manuscript forms.

## Method

### 1. Complete occurrence ledger

Record every word, phrase, name, title, ethnonym, loan, translated proverb, script reference and
culturally encoded display (colour, body marking, gesture) with `file:line`, language, speaker, POV,
formatting and first-use status, plus synchronized occurrences in outlines, notes and tracking docs.
Search exact and case-folded variants after the close read. Classify: native vocabulary/inflection;
loan or sacred/taboo form; personal/place name; people/species term; technology term; untranslated
concept; gated proper term. **Gate:** 100% inventoried; no sampling. A catalogue miss is `unknown`
until context proves an error.

### 2. Knowledge gates before linguistics

For each proper term, verify that the speaker or POV holder has crossed the reveal point at this chapter,
not merely that the term appeared earlier in the book. Distinguish narrator labels, device translations,
overheard language and character knowledge; a forbidden proper name from an ordinary lowercase
adjective; a mistaken in-scene impression from an omniscient doc statement. **Gate:** zero premature
terms. A correct word used too early is still a required finding. Never fix a gate by adding exposition
the project forbids.

### 3. Attestation and meaning

Search the curated vocabulary, nested vocabularies, grammar, examples, culture and character files.
Verify lemma, sense, semantic role, register, taboo status and who may say it. Ordinary homophony and
synonymy are not contradictions (natural languages have both): report a collision only when the
forms clash in grammar, meaning, usage or explicit canon; otherwise note an unexplained homonym as a
`QUERY`, and follow the project's own ruling where it has one. **Gate:** every form attested or transparently derivable from attested
productive rules; otherwise mark `UNATTESTED` and do not guess its intended meaning.

### 4. Orthography, phonology, phonotactics

Compare every segment and diacritic with the inventory and romanization. Parse syllables; test onsets,
nuclei, codas, clusters, stress, length, tone and apostrophe conventions. Names and compounds get no
automatic exemption. IPA distinguishes sounds; phonotactics licenses combinations [S1–S4]. **Gate:** zero
unexplained violations; accept only a canon-labelled loan, archaism, dialect form or irregular name.

### 5. Morphology

Segment root plus derivational and inflectional material. Check morpheme order, affix direction, fusion,
exponence, agreement, number, case, TAM, evidentiality, negation and allomorphy [S5–S8]. For harmony,
identify feature, trigger, target, direction, domain and neutral/opaque vowels before judging [S4].
**Gate:** every surface form has a complete attested parse that computes the claimed meaning; obligatory
categories appear exactly once unless canon licenses zero or cumulative exponence.

### 6. Syntax and pragmatics

Test word order, adpositions, questions, negation, argument roles and clause type. Then honorifics,
collective pronouns, ritual formulae, politeness, consent, insult and cultural ownership; formal vs
casual name forms against scene register. Grammatical is not the same as appropriate. **Gate:** literal
composition, idiomatic gloss, speech act, speaker and scene function agree.

### 7. Reader-facing presentation

Name what the switch does: precision, identity, hierarchy, intimacy, ritual, mood or verisimilitude
[S15–S16]. A plot-critical meaning needs one clear access path: action/reaction, apposition, integrated
translation, echo, or (only when mystery is intended) delayed inference; a glossary is never the sole
support [S14]. Follow house typography; do not "correct" it to MLA [S13]. **Gate:** no plot-critical
opaque switch, duplicate teaching, dictionary interruption, or gloss that contradicts the conlang.

### 8. Sweep the class

For every finding, search the whole scope for the same token and the same rule failure; list all
instances together. Re-check replacement text against canon rather than constructing it ad hoc. When
only one occurrence fails, cite the inventory that proves it isolated.

### 9. Negative audit (no conlang tokens)

Prove the negative with three inventories: gated proper terms and capitalization variants (inspect
lowercase matches in context); coined labels for creatures, objects and crews (who coins each, and
whether this scene may introduce it); personal-name and address variants against register rules. Report
zero-hit gated terms as a compact list. Never conclude a vague "conlang clean" without the gate evidence.

## Report format

```markdown
- **[severity] Class — form**
  - Instances: `file:line`, ...
  - Canon: `path §heading` or `path:line`
  - Error: spelling | phonology | morphology | syntax | meaning | register | gating | readability
  - Why: literal parse and violated rule
  - Correct form/fix: exact canon form, or `canon decision required`
```
`required` for wrong form, meaning, register or gating, or a plot-critical utterance whose action cannot
be understood; `suggestion` only for reader-load choices that stay canon-correct. If all checks pass, say
which terms were reviewed and why each reveal is permitted.

## Naturalism tests (canon design work only)

Never use these to retrofit manuscript forms.
- **System coherence:** inventory, phonotactics, morphology and syntax predict forms instead of being
  backfilled from isolated lines [S2–S6, S11].
- **Diachronic cause:** irregularity traces to sound change, borrowing, analogy or grammaticalization [S9–S12].
- **Typological independence:** do not assume English categories [S5–S8].
- **Culture coupling:** names, taboo, honorifics, metaphors and registers follow speaker history [S10, S12].
- **Documentation:** inventory, phonotactics, paradigms, interlinear glosses (Leipzig rules standardize
  presentation, not analysis) and idiomatic translations [S7].

## Project specifics

The project's own skill supplies: which languages exist and where their canon lives, the syllable
templates and rules to test, reveal epochs for gated terms, house typography for names and phrases,
canon replacement expressions for provisional forms, and known contradictions in the language files.
