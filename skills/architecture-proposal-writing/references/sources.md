# Sources for architecture proposal writing

The evidence behind each rule in SKILL.md. No rule rests on a search snippet: sources that could not be fetched
in full are marked "unverified, not load-bearing" and kept only as background.

<a id="answer-first"></a>
## Answer first, scanning and plain language

| Claim | Source | Year | Type |
|---|---|---|---|
| Build "a single pyramid of ideas… held together by a single thought" and guide the reader down it | Barbara Minto, Pyramid Principle concept https://www.barbaraminto.com/concept | ongoing | practitioner |
| "Start content with the most important piece of information so readers can get the main point, regardless of how much they read" | NN/g, Inverted Pyramid https://www.nngroup.com/articles/inverted-pyramid/ | — | practitioner research |
| Users mostly scan (F-pattern, layer-cake); put key points in the first two paragraphs; start headings with information-carrying words; bold key phrases sparingly | NN/g, F-Shaped Pattern https://www.nngroup.com/articles/f-shaped-pattern-reading-web-content/ | 2006 / 2017 | eyetracking research |
| "Put the most important information first"; break text up with headings | GOV.UK, clear structure https://guidance.publishing.service.gov.uk/writing-to-gov-uk-standards/writing-guidelines/clear-structure | ongoing | official guidance |
| Provide summaries of long documents, chunk content, make purpose clear, short critical paths | W3C COGA, Making Content Usable for People with Cognitive and Learning Disabilities https://www.w3.org/TR/coga-usable/ | 2021 | standard (WG Note) |
| One topic per paragraph; aim of the chunk at its start; one point per sentence | W3C COGA pattern "Keep Text Succinct" https://w3.org/WAI/WCAG2/supplemental/patterns/o3p05-succinct-text | 2021 | standard |
| Show only the most important options first; reveal the rest on request | NN/g, Progressive Disclosure https://www.nngroup.com/articles/progressive-disclosure/ | 2006 | practitioner research |
| Plain language starts with "write for your audience" | digital.gov plain-language principles https://digital.gov/guides/plain-language/principles | ongoing | official guidance |
| Separate reference from explanation | Diátaxis https://diataxis.fr/ | ongoing | practitioner framework |

No authoritative ADHD-specific documentation standard was found. W3C COGA (which covers attention), NN/g and
plain-language guidance are the evidence base. The owner-specific shaping lives in the shared `i-have-adhd` skill.

## Two artifacts and document shapes

| Claim | Source | Year | Type |
|---|---|---|---|
| Mini design docs of one to three pages; "Alternatives considered" is "one of the most important" sections; goals *and* non-goals; skip the doc when there are no trade-offs | Malte Ubl, Design Docs at Google https://www.industrialempathy.com/posts/design-docs-at-google/ | 2020 | practitioner |
| Narrative six-page memos read silently at the start of meetings | Amazon 2017 shareholder letter https://www.aboutamazon.com/news/company-news/2017-letter-to-shareholders | 2018 | official |
| Press release and FAQ written before building | Working Backwards, PR/FAQ https://workingbackwards.com/concepts/the-amazon-working-backwards-pr-faq-process/ | — | practitioner |
| Timely rather than polished notes; discussion lifecycle states | Oxide RFD 1 https://rfd.shared.oxide.computer/rfd/0001 | 2019+ | practitioner standard |
| Separate drawbacks, rationale and alternatives, and prior art sections | Rust RFC template https://github.com/rust-lang/rfcs/blob/master/0000-template.md | ongoing | OSS template |
| Templates should encode the questions reviewers keep asking | Orosz, Scaling Engineering Teams via RFCs https://blog.pragmaticengineer.com/scaling-engineering-teams-via-writing-things-down-rfcs/ | 2018 / 2022 | practitioner |

## Decision records

| Claim | Source | Year | Type |
|---|---|---|---|
| ADR origin: short records with context, decision, consequences | Nygard https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions | 2011 | practitioner |
| MADR: drivers, considered options, outcome, consequences, confirmation; full, minimal and bare variants | MADR https://adr.github.io/madr/ · repo https://github.com/adr/madr | ongoing | OSS template |
| Template catalogue (Nygard, Y-statement, Tyree/Akerman, arc42…) | https://adr.github.io/adr-templates/ | ongoing | community |
| Y-statement form | Design Practice Repository https://socadk.github.io/design-practice-repository/artifact-templates/DPR-ArchitecturalDecisionRecordYForm.html ; Zimmermann https://ozimmer.ch/practices/2020/04/27/ArchitectureDecisionMaking.html | 2020 | practitioner |
| Preserve history by superseding, not editing | AWS Prescriptive Guidance https://docs.aws.amazon.com/prescriptive-guidance/latest/architectural-decision-records/best-practices.html | ongoing | official |
| Government ADR practice | GDS Way https://gds-way.digital.cabinet-office.gov.uk/standards/architecture-decisions.html ; UK ADR Framework https://www.gov.uk/government/publications/architectural-decision-record-framework/architectural-decision-record-framework | 2025 | official |
| When to write an ADR | Spotify https://engineering.atspotify.com/when-should-i-write-an-architecture-decision-record | 2020 | practitioner |
| Writing an ADR "makes design decisions real"; a commitment device | Keeling https://keeling.dev/essays/psychology-of-architecture-decision-records/ | 2022 | paper/essay |
| Keep records in version control, in the application's repository; consequences "both positive and negative"; Proposed → Accepted lifecycle with reviewers from affected disciplines | GDS Way https://gds-way.digital.cabinet-office.gov.uk/standards/architecture-decisions.html ; Thoughtworks Radar, lightweight ADRs https://thoughtworks.com/radar/techniques/lightweight-architecture-decision-records | 2025 / ongoing | official / practitioner |
| Status and consequences sections; supersede rather than rewrite | MADR https://adr.github.io/madr/ ; AWS Prescriptive Guidance https://docs.aws.amazon.com/prescriptive-guidance/latest/architectural-decision-records/best-practices.html | ongoing | OSS template / official |
| Match the repository's existing ADR convention before introducing MADR | No external source; judgement (consistency with the repository the record lives in) | — | judgement |

<a id="tables-and-charts"></a>
## Tables, matrices and charts

| Claim | Source | Year | Type |
|---|---|---|---|
| Decision matrices help clarify criteria but mislead when "treated as machines that automatically produce the right strategic choice"; options "rarely comparable on a single scale" | Sustainable Catalyst https://sustainablecatalyst.com/decision-matrices-and-their-limits/ | — | secondary |
| Background only: pitfalls in decision-matrix construction | Mullur, Mattson & Messac, "Pitfalls of the Typical Construction of Decision Matrices for Concept Selection" (AIAA) https://doi.org/10.2514/6.2003-466 (metadata checked; full text behind an access wall; not load-bearing) | 2003 | paper |
| Background only: uses and misuses of MCDA | Steele, Carmel, Cross & Wilcox, "Uses and Misuses of Multicriteria Decision Analysis (MCDA) in Environmental Decision Making" (Risk Analysis) https://doi.org/10.1111/j.1539-6924.2008.01130.x (abstract read; not load-bearing) | 2008 | paper |
| For big decisions, evaluate against quality-attribute scenarios, sensitivity points and trade-offs | SEI ATAM collection https://www.sei.cmu.edu/library/architecture-tradeoff-analysis-method-collection/ | ongoing | official/research |
| Radar data "would usually be shown as a bar graph" (sufficient on its own for the radar-chart caution) | Stephen Few https://www.perceptualedge.com/articles/dmreview/radar_graphs.pdf | 2005 | practitioner |
| Cost curves as a trade-off device | No source found; design suggestion | — | — |

## Diagrams

| Claim | Source | Year | Type |
|---|---|---|---|
| C4: context, container, component, code, plus landscape, dynamic and deployment; notation-independent | https://c4model.com/ · https://c4model.com/faq | ongoing | practitioner standard |
| arc42 sections incl. context and scope, quality requirements, risks | https://arc42.org/overview | ongoing | template |
| Mermaid C4 syntax | https://mermaid.js.org/syntax/c4.html | ongoing | official docs |
| Data-flow diagrams show "trust boundaries, data flows, data stores, processes, and the external entities"; then STRIDE | OWASP Threat Modeling Cheat Sheet https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html | ongoing | official (OWASP) |
| Use the existing diagram convention; confirm it renders in the target | No external source; judgement (renderer support varies by repository host, document tool, PDF and email) | — | judgement |
| Adjacent text equivalent for each diagram | WCAG 2.2, success criterion 1.1.1 Non-text Content (text alternatives) https://www.w3.org/TR/WCAG22/ | 2023 | standard |
| Timeout defaults only for delegated, reversible, bounded-risk decisions; silence is not approval | No external source; judgement, consistent with one-way/two-way door classification (Bezos 2015 letter, see `architecture-decision-making` H1) | — | judgement |
| Confidence words with an evidence rubric instead of uncalibrated percentages | Shared rubric in `architecture-research-and-verification` step 7 (judgement) | — | judgement |

## Borrowed ideas and attribution (licensing only, not evidence)

These third-party skills inspired structure. They are credited for licensing; no rule depends on them.

- Contested-choice box: adapted from the "Why & What box" in riekelt `writing-design-docs` (MIT)
  https://github.com/riekelt/technical-writer/blob/main/plugins/technical-writer/skills/writing-design-docs/SKILL.md
- Tiered reading with "start here / recommended / further reading" bands and last-verified dates: adapted from
  depuschm/architecture-guidelines (CC-BY-4.0) https://github.com/depuschm/architecture-guidelines. Ideas adapted;
  no text copied.
- Diagram communication checklist: adapted from magnus919 (MIT)
  https://github.com/magnus919/agent-skills/blob/main/c4-diagramming/references/technical-diagram-communication.md
- ADR conventions (match existing convention; Proposed-only drafting; mandatory downsides) were also seen in
  addyosmani `documentation-and-adrs` (MIT)
  https://github.com/addyosmani/agent-skills/blob/main/skills/documentation-and-adrs/SKILL.md , owl-registry
  (Apache-2.0) https://github.com/MichaelWeissDEV/owl-registry/blob/main/skills/architecture-decision-records/SKILL.md
  and sbrudz (MIT) https://github.com/sbrudz/agent-skills/blob/main/skills/architecture-decision-records/SKILL.md ;
  the rules themselves rest on MADR, AWS and GDS guidance above.
- No text was copied from repositories without a clear licence.
