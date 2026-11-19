# Canon reading list

Tiered reading for architecture decision-making. **Essential**: read or know well. **Deep dive**: read
when the topic is in play. **Reference**: look things up. Year is first publication of the cited edition.
Editions change, so check the publisher or author page for the current one before citing an edition.

Type key: book · paper (peer-reviewed or technical report) · post (blog or essay) · standard/template · talk · site.

## Essential

| Title | Author | Year | Type | Link | Why |
|---|---|---|---|---|---|
| Fundamentals of Software Architecture (2nd ed.) | Richards, Ford | 2025 | book | https://nealford.com/books/SAF2e.html | Characteristics, styles, trade-off thinking ("everything is a trade-off") |
| Software Architecture: The Hard Parts | Ford, Richards, Sadalage, Dehghani | 2021 | book | https://nealford.com/books/ahp.html | Trade-off analysis for distributed systems |
| Building Evolutionary Architectures (2nd ed.) | Ford, Parsons, Kua, Sadalage | 2022 | book | https://thoughtworks.com/en-us/insights/books/building-evolutionaryarchitectures-second-edition | Fitness functions, incremental change |
| Software Architecture in Practice (4th ed.) | Bass, Clements, Kazman | 2021 | book | http://oreilly.com/library/view/-/9780136885979/ | Quality scenarios, tactics, ATAM, lightweight evaluation |
| Just Enough Software Architecture | Fairbanks | 2010 | book | https://www.georgefairbanks.com/book/ | Risk-driven design effort |
| A Philosophy of Software Design (2nd ed.) | Ousterhout | 2021 | book | https://web.stanford.edu/~ouster/cgi-bin/aposd.php | Complexity, deep modules |
| Designing Data-Intensive Applications (2nd ed.) | Kleppmann, Riccomini | 2026 | book | https://martin.kleppmann.com/2026/03/24/designing-data-intensive-applications-2e.html | Data-system trade-offs |
| The Software Architect Elevator | Hohpe | 2020 | book | https://architectelevator.com/book | The architect's role; decision discipline |
| Architecture: Selling Options | Hohpe | 2016 | post | https://architectelevator.com/architecture/architecture-options/ | Architecture as options; value rises with volatility |
| Documenting Architecture Decisions | Nygard | 2011 | post | https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions | Origin of ADRs |
| MADR | adr.github.io | ongoing | template | https://adr.github.io/madr/ | Default decision-record template |
| Architectural Decisions: The Making Of | Zimmermann | 2020 | post | https://ozimmer.ch/practices/2020/04/27/ArchitectureDecisionMaking.html | Y-statements; what not to log |
| Seven AD-Making Fallacies | Zimmermann | 2025 | post | https://ozimmer.ch/practices/2025/09/01/ADMFallacies.html | Reasoning-error checklist |
| Design Docs at Google | Ubl | 2020 | post | https://industrialempathy.com/posts/design-docs-at-google | Goals and non-goals; alternatives considered |
| Yagni | Fowler | 2015 | post | https://martinfowler.com/bliki/Yagni.html | Cost of presumptive features |
| Sacrificial Architecture | Fowler | 2014 | post | https://martinfowler.com/bliki/SacrificialArchitecture.html | Planned replacement; the 10×/100× case is an example, not a sizing rule |
| Monolith First / Don't start with a monolith | Fowler / Tilkov | 2015 | post | https://martinfowler.com/bliki/MonolithFirst.html · https://martinfowler.com/articles/dont-start-monolith.html | Read as a pair |
| Choose Boring Technology | McKinley | 2015 | post | https://mcfunley.com/choose-boring-technology | Innovation tokens |
| 2015 Letter to Shareholders | Bezos | 2016 | letter | https://s2.q4cdn.com/299287126/files/doc_financials/annual/2015-Letter-to-Shareholders.PDF | One-way vs two-way doors |
| Conway's Law / How Do Committees Invent? | Fowler / Conway | 2022 / 1968 | post / paper | https://www.martinfowler.com/bliki/ConwaysLaw.html · https://www.melconway.com/Home/Committees_Paper.html | Organisation as design constraint |
| Team Topologies key concepts | Skelton, Pais | 2019 | site | https://teamtopologies.com/key-concepts | Team types, interaction modes |
| Canon 3X: Explore/Expand/Extract | Beck | 2026 | post | https://newsletter.kentbeck.com/p/canon-3x-exploreexpandextract | Phase-appropriate strategy |
| Debiasing Architectural Decision-Making | Borowa et al. | 2025 | paper | https://arxiv.org/abs/2502.04011 | Anchoring and optimism reduced by practice (small experiment: 16 students, 20 practitioners) |
| C4 model | Brown | ongoing | site | https://c4model.com/ | Zoomable diagrams |
| Wardley on evolution | Wardley | 2016 | book excerpt | https://www.wardleymaps.com/book-passages/wardley-on-evolution | Build / buy / rent by evolution stage |
| OSS and lock-in | Hohpe | 2019 | post | https://martinfowler.com/articles/oss-lockin.html | Lock-in priced as switching cost |

## Deep dive

| Title | Author | Year | Type | Link | Why |
|---|---|---|---|---|---|
| ATAM (CMU/SEI-2000-TR-004) | Kazman, Klein, Clements | 2000 | paper | https://www.sei.cmu.edu/documents/629/2000_005_001_13706.pdf | Utility trees, sensitivity and trade-off points |
| Risk Themes from ATAM Data | Bass, Nord, Wood | 2006 | paper | https://sei.cmu.edu/documents/2902/2006_017_001_22523.pdf | Omission risks dominate |
| CBAM | Kazman et al. | 2003 | paper | https://sei.cmu.edu/documents/179/2003_019_001_29571.pdf | Economic comparison of options |
| Lightweight Architecture Evaluation (notes) | Sicoie | ~2023 | post | https://robertsicoie.com/blog/lae | A couple of hours, peer-run |
| An Ontology of Architectural Design Decisions | Kruchten | 2004 | paper | https://philippe.kruchten.com/wp-content/uploads/2009/07/kruchten-2004-design-decisions.pdf | Decision kinds, including bans |
| The Psychology of ADRs | Keeling | 2022 | paper | https://keeling.dev/essays/psychology-of-architecture-decision-records/ | ADRs as commitment devices |
| Using ADRs in OSS (MSR study) | Buchgeher et al. | 2023 | paper | https://research.jku.at/en/publications/using-architecture-decision-records-in-open-source-projects-an-ms | Adoption is shallow; plan for ownership |
| Exploratory Study of NDM in Architecture | Power, Wirfs-Brock | 2019 | paper | https://wirfs-brock.com/rebecca/papers/ExploratoryStudyOfNDM | When intuition is appropriate |
| Software Developer Productivity Loss Due to TD | Besker, Martini, Bosch | 2019 | paper | https://research.chalmers.se/publication/511450/file/511450_Fulltext.pdf | ~23% of developer time lost to debt |
| RFD 1: Requests for Discussion | Oxide | 2019 | standard | https://rfd.shared.oxide.computer/rfd/0001 | Timely-not-polished writing culture |
| Scaling Engineering Teams via RFCs | Orosz | 2020 | post | https://blog.pragmaticengineer.com/scaling-engineering-teams-via-writing-things-down-rfcs | RFC process at scale |
| Against Innovation Tokens | Glyph | 2024 | post | https://blog.glyph.im/2024/07/against-innovation-tokens.html | Critique: measure operational overhead |
| Architecture for Flow, ch. 2 | Kaiser | 2025 | book excerpt | https://informit.com/articles/article.aspx?p=3222355&seqNum=4 | Build/buy with DDD + Wardley + Team Topologies |
| Cost of Delay | Black Swan Farming | ongoing | post | https://blackswanfarming.com/cost-of-delay/ | Time value of decisions |
| The Frugal Architect | Vogels | 2023 | site | https://thefrugalarchitect.com/ | Cost as a requirement |
| Why we're leaving the cloud | DHH | 2022 | post | https://world.hey.com/dhh/why-we-re-leaving-the-cloud-654b47e0 | Load shape decides rent vs own |
| Microservice Trade-Offs | Fowler | 2015 | post | https://martinfowler.com/articles/microservice-trade-offs.html | Ledger of benefits and costs |
| Fitness function-driven development | ThoughtWorks | ~2019 | post | https://thoughtworks.com/insights/articles/fitness-function-driven-development | Eliciting fitness functions |
| Software Architecture Monday | Richards | ongoing | talk series | https://www.developertoarchitect.com/lessons/ | Short lessons (risk storming, analysis paralysis) |
| Architectural Katas | Neward | ongoing | site | https://www.architecturalkatas.com/ | Deliberate practice |

## Reference

| Title | Year | Type | Link |
|---|---|---|---|
| arc42 template | ongoing | template | https://arc42.org/overview |
| ISO/IEC 25010:2023 (via arc42 quality model) | 2023 | standard | https://quality.arc42.org/standards/iso-25010 |
| AD practices index | ongoing | site | https://adr.github.io/ad-practices |
| SEI Architecture Analysis Techniques and When to Use Them | 2002 | paper | https://www.sei.cmu.edu/library/sei-architecture-analysis-techniques-and-when-to-use-them/ |
| Google SRE book: PRR chapter | 2016 | book chapter | https://sre.google/sre-book/evolving-sre-engagement-model |
| Technical Debt Quadrant | 2009 | post | https://www.martinfowler.com/bliki/TechnicalDebtQuadrant.html |
| ThoughtWorks Technology Radar (current blips; one consultancy's opinion) | ongoing | site | https://www.thoughtworks.com/radar |
| AWS / Azure / Google Cloud well-architected frameworks | ongoing | site | https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html · https://learn.microsoft.com/en-us/azure/well-architected/pillars · https://cloud.google.com/architecture/framework |

Where newer research appears: ICSA papers via https://conf.researchr.org ; see
`architecture-research-and-verification` for how to search and appraise.
