# Decision heuristics

Rules of thumb for architecture decisions. Each states the rule, the value it protects, when to apply it,
when **not** to, and its source. These are heuristics, not laws: when one fights the value it serves in a
specific case, the value wins, and the decision record says why.

Values: **Fit**, **Simplicity**, **Evolvability**, **Cost**, **Traceability** (see SKILL.md).

---

<a id="h1"></a>
### H1 Classify reversibility before choosing process weight
- **Rule:** decide whether this is a one-way door (consequential, nearly irreversible) or a two-way door
  (changeable). Spend process in proportion to irreversibility × blast radius.
- **Protects:** Cost (no wasted ceremony), Fit (no careless one-way commitments).
- **Not when:** never skip the classification itself. But don't treat "reversible in theory" as two-way if
  data migration, public contracts or retraining make reversal expensive. Estimate the real switching cost.
- **Source:** Bezos, 2015 shareholder letter. Heavy process on Type 2 decisions brings "slowness,
  unthoughtful risk aversion". https://s2.q4cdn.com/299287126/files/doc_financials/annual/2015-Letter-to-Shareholders.PDF

<a id="h2"></a>
### H2 Generate at least three genuinely different options before evaluating
- **Rule:** include keep-current or extend-existing, and buy/managed. Write the list before favouring any option.
- **Protects:** Fit; counters anchoring and satisficing.
- **Not when:** constraints leave one option. Say so and show why, rather than inventing straw men.
- **Sources:** Google design docs ("Alternatives considered" is "one of the most important" sections)
  https://industrialempathy.com/posts/design-docs-at-google ; Borowa et al. 2025 (anchoring: preferring "the first
  architectural solution that they came up with") https://arxiv.org/abs/2502.04011 ; Kahneman, Lovallo &
  Sibony 2011, checklist Q5 ("insist that people submit at least one or two alternatives")
  https://hbr.org/2011/06/the-big-idea-before-you-make-that-big-decision ; Zimmermann (reject "I do not know
  any alternative") https://ozimmer.ch/practices/2020/04/27/ArchitectureDecisionMaking.html

<a id="h3"></a>
### H3 Write qualities as scenarios with response measures, ranked in a utility tree
- **Protects:** Fit; makes trade-offs discussable.
- **Not when:** early exploration where numbers are unknowable. Record the assumption and a revisit trigger
  instead of fake precision.
- **Sources:** https://people.ece.ubc.ca/matei/EECE417/BASS/ch04lev1sec3.html ; ATAM
  https://www.sei.cmu.edu/documents/629/2000_005_001_13706.pdf

<a id="h4"></a>
### H4 Decide at the last responsible moment, but buy options early where volatility is high
- **Rule:** delay commitment until "failing to make a decision eliminates an important alternative". Where
  uncertainty is high, pay a little now for a seam that keeps the choice open. Options are worth more as
  volatility rises.
- **Protects:** Evolvability, Cost.
- **Not when:** the decision's last responsible moment is now (data model of record, security and privacy,
  regulatory, public APIs). Deferral also has a cost of delay.
- **Sources:** https://blog.codinghorror.com/the-last-responsible-moment/ ;
  https://architectelevator.com/architecture/architecture-options/ ; https://blackswanfarming.com/cost-of-delay/

<a id="h5"></a>
### H5 YAGNI applies to speculative capabilities, never to malleability
- **Rule:** don't build presumptive features. They cost build, delay, carry and repair. Always invest in what
  keeps change cheap: tests, modularity, refactoring, continuous delivery.
- **Protects:** Simplicity, Evolvability.
- **Not when:** adding the capability later is clearly much more expensive than adding it now (e.g. tenant
  isolation in the data model, auditability, i18n of stored data). Compare build-now cost with refactor-later cost.
- **Source:** Fowler: "Yagni only applies to capabilities built into the software to support a presumptive
  feature, it does not apply to effort to make the software easier to modify." https://martinfowler.com/bliki/Yagni.html

<a id="h6"></a>
### H6 Size from forecast scenarios; sacrificial architecture is one way to do it
- **Rule:** the forecast horizon, peak shape and response scenarios (SKILL.md step 3) set capacity. One
  pattern for fast-growing products is sacrificial architecture: one well-known case designed for about 10×
  growth and planned a rewrite before about 100×. Treat that ratio as an example of the pattern, not a
  universal sizing rule.
- **Protects:** Simplicity now, Evolvability later.
- **How to spot misuse:** "10×" appears in a design with no load forecast or peak scenario behind it.
- **Not when:** contractual or regulatory capacity commitments; safety-relevant load; a hard launch or event
  peak; fat-tailed or viral growth where the plausible peak is far above 10×; a trivial, bounded workload
  where 10× is over-design. Hard-to-replace parts (persistent data formats, external contracts) deserve
  longer horizons. "Sacrificial" never licenses poor internal modularity.
- **Source:** Fowler, citing Jeff Dean. https://martinfowler.com/bliki/SacrificialArchitecture.html

<a id="h7"></a>
### H7 Modular monolith first; extract services when boundaries are proven
- **Rule:** modularity is the goal. Deployment topology is a separate, later decision.
- **Protects:** Simplicity, Cost, Evolvability.
- **Not when:** the domain is very well understood (e.g. a replacement system), subsystems need independent
  scaling or regulation, or several teams work on it from day one.
- **Sources:** https://martinfowler.com/bliki/MonolithFirst.html ; counter-view:
  https://martinfowler.com/articles/dont-start-monolith.html ; one codebase with enforced boundaries:
  https://shopify.engineering/deconstructing-monolith-designing-software-maximizes-developer-productivity ;
  trade-off ledger: https://martinfowler.com/articles/microservice-trade-offs.html

<a id="h8"></a>
### H8 Budget innovation tokens; prefer what you already operate
- **Rule:** each new technology carries unknown unknowns, operational load and cognitive overhead. Spend
  novelty where it is the differentiator, or where the boring option provably fails a requirement. A new tool
  that overlaps an existing one needs a committed migration plan.
- **Protects:** Cost, Simplicity.
- **Not when:** the boring option has a large, *specific* operating cost. Judge by operational overhead, not
  novelty. The point is to budget, not forbid.
- **Sources:** https://mcfunley.com/choose-boring-technology ; critique: https://blog.glyph.im/2024/07/against-innovation-tokens.html

<a id="h9"></a>
### H9 Build what differentiates; buy products; rent commodities
- **Protects:** Cost, Fit.
- **Not when:** a commodity is your core business (build it), or a bought product would need heavy
  customisation (reconsider buying).
- **Sources:** https://www.wardleymaps.com/book-passages/wardley-on-evolution ;
  https://informit.com/articles/article.aspx?p=3222355&seqNum=4

<a id="h10"></a>
### H10 Match architecture and team structure
- **Rule:** boundaries the organisation can't support will erode; organisation choices pre-commit design.
- **Protects:** Fit, Evolvability.
- **Not when:** don't reorganise people to fit a diagram without addressing their cognitive load and flow.
- **Sources:** https://www.melconway.com/Home/Committees_Paper.html ; https://www.martinfowler.com/bliki/ConwaysLaw.html ;
  https://teamtopologies.com/key-concepts

<a id="h11"></a>
### H11 Scale design rigour to risk
- **Rule:** stop designing when the identified risks are mitigated; no architecture at all is not the answer either.
- **Protects:** Cost, Fit.
- **Not when:** high-consequence domains (safety, money, privacy) set a much lower bar for "small risk".
- **Sources:** Fairbanks, risk-driven model https://www.georgefairbanks.com/book/ ; Brown, just enough
  up-front design https://www.infoq.com/news/2014/05/minimal-architecture-design

<a id="h12"></a>
### H12 Turn important quality goals into fitness functions
- **Protects:** Fit over time; Traceability.
- **Not when:** the quality can't be measured objectively. Don't fake a metric ("readability"); use scenario
  reviews. Beware metrics that become targets.
- **Sources:** https://www.infoq.com/articles/fitness-functions-architecture/ ;
  https://thoughtworks.com/insights/articles/fitness-function-driven-development

<a id="h13"></a>
### H13 Record only architecturally significant decisions, including bans
- **Rule:** each record names rejected options and accepted downsides. A practical gate: record it if at least
  two hold: multi-component, constrains future work, real alternatives existed, costly to reverse.
- **Protects:** Traceability without noise.
- **Not when:** trivia (more than ~100 decisions puts readers to sleep), or decisions fully dictated by a platform
  standard (reference the standard instead).
- **Sources:** https://ozimmer.ch/practices/2020/04/27/ArchitectureDecisionMaking.html ;
  https://philippe.kruchten.com/wp-content/uploads/2009/07/kruchten-2004-design-decisions.pdf ; gate idea from
  https://github.com/sbrudz/agent-skills/blob/main/skills/architecture-decision-records/SKILL.md (MIT)

<a id="h14"></a>
### H14 Write a design document only when real trade-offs exist
- **Not when:** it would be an implementation manual with no alternatives; link a prototype instead.
- **Source:** https://industrialempathy.com/posts/design-docs-at-google

<a id="h15"></a>
### H15 Match the product phase
- **Rule:** Explore = cheap and reversible; Expand = remove the next bottleneck; Extract = efficiency and rigour.
- **Protects:** Cost, Fit.
- **Not when:** don't import extract-phase governance into exploration, and don't run a scaling product on exploration shortcuts.
- **Source:** https://newsletter.kentbeck.com/p/canon-3x-exploreexpandextract

<a id="h16"></a>
### H16 Choose the decision mode by context
- **Rule:** compare alternatives analytically when time allows. Recognition-primed (expert pattern-match)
  decisions are fine when urgent or in familiar, high-feedback territory. Record them and revisit.
- **Not when:** urgency is used as an excuse to skip alternatives on a one-way door. Architecture often lacks the
  regular, fast feedback that makes intuition trustworthy.
- **Sources:** https://wirfs-brock.com/rebecca/papers/ExploratoryStudyOfNDM ; https://psycnet.apa.org/record/2009-13007-001

<a id="h17"></a>
### H17 Compare qualitatively; don't trust weighted decimal totals
- **Rule:** use ✓ / ~ / ✗ (or H/M/L) with a reason per cell, and state the one or two deciding criteria in
  words. If weights are used, show the sensitivity ("A wins unless cost weight > 40%"). Score criteria
  independently before forming the holistic judgment.
- **Protects:** Fit, honesty about uncertainty.
- **Not when:** high-stakes economic comparisons can justify a structured cost-benefit method (CBAM), with its
  uncertainty made explicit.
- **Sources:** matrices are dangerous when "treated as machines that automatically produce the right strategic
  choice" (secondary) https://sustainablecatalyst.com/decision-matrices-and-their-limits/ ; AIAA 2003 pitfalls
  paper (abstract not fetched) https://doi.org/10.2514/6.2003-466 ; Mediating Assessments Protocol summary
  https://theuncertaintyproject.org/tools/the-mediating-assessments-protocol ; CBAM https://sei.cmu.edu/documents/179/2003_019_001_29571.pdf

<a id="h18"></a>
### H18 Price lock-in as an option's strike price
- **Rule:** lock-in is a spectrum, not yes/no. Plot options by switching cost × unique utility. Avoiding one
  lock-in usually creates another. "Complexity can be the biggest price you pay for reducing lock-in."
- **Protects:** Cost, Evolvability.
- **Not when:** lock-in is genuinely existential (a single-supplier dependency for a regulated core). Then
  pay for portability deliberately.
- **Source:** Hohpe https://martinfowler.com/articles/oss-lockin.html

<a id="h19"></a>
### H19 Let load shape and team size drive rent vs own
- **Rule:** rented elastic infrastructure shines for tiny or early apps and for highly irregular load. Steady,
  predictable load at scale, with existing operations skill, may favour owning. Cloud is an innovation premium,
  not a discount.
- **Protects:** Cost.
- **Not when:** growth is uncertain (elastic capacity absorbs surprises) or there are no operations staff.
- **Sources:** https://world.hey.com/dhh/why-we-re-leaving-the-cloud-654b47e0 ;
  https://www.lastweekinaws.com/blog/cloud-repatriation-is-getting-complicated/

<a id="h20"></a>
### H20 Treat cost as a requirement, measured per unit of value
- **Rule:** state a cost target as an NFR from day one. Track cost per business unit (per user, per order), not
  only total spend.
- **Sources:** https://thefrugalarchitect.com/ ; https://www.finops.org/framework/principles/ ;
  https://www.finops.org/framework/capabilities/unit-economics

<a id="h21"></a>
### H21 Count operator hours; toil dominates at small scale
- **Rule:** for each option, estimate recurring manual work (patching, upgrades, backups, restores, on-call).
  For one person, every single point of failure means that person is the backup.
- **Not when:** the work is genuinely automated and its automation is itself maintained.
- **Sources:** https://sre.google/sre-book/eliminating-toil/ ; https://imad.website/writing/solo-developer-playbook/

<a id="h22"></a>
### H22 Every additional datastore or platform needs a written justification
- **Rule:** map each need to a feature of what you already run before adding a specialised system. Add one only
  for a measured gap on your own workload.
- **Not when:** a measured gap exists. Note that "use one database for everything" writing is advocacy; check
  it against your workload.
- **Sources:** https://www.amazingcto.com/postgres-for-everything/ (advocacy) ; https://www.sqlite.org/whentouse.html ;
  https://mcfunley.com/choose-boring-technology

<a id="h23"></a>
### H23 Adopt heavyweight platforms when their problems are your problems
- **Example:** an orchestration platform pays off with many services and teams and someone to own it. For a
  small team with few services, its complexity tax usually exceeds its benefit.
- **Sources:** https://www.arp242.net/dont-need-k8s.html ; https://k8s.af/ ; https://kamal-deploy.org/

<a id="h24"></a>
### H24 Plan for the tail, not the mean
- **Rule:** IT project risk is fat-tailed. Ask what the bad-tail outcome looks like and whether you can survive it.
  Prefer incremental replacement over big-bang.
- **Not when:** small, contained, reversible changes with a capped downside.
- **Source:** Flyvbjerg & Budzier: across 1,471 IT projects, about one in six had an average cost overrun of
  200%. https://arxiv.org/abs/1304.0265

<a id="h25"></a>
### H25 Make the change easy, then make the easy change
- **Rule:** architecture work is often preparatory refactoring, done just before the feature that needs it.
- **Source:** https://dtprinciples.blogspot.com/2019/08/becks-law.html

<a id="h26"></a>
### H26 Grow complex systems from simple working ones
- **Source:** Gall's law (1975, secondary page) https://en.wikipedia.org/wiki/Gall%27s_law

<a id="h27"></a>
### H27 Multi-cloud needs a named driver
- **Rule:** "Hybrid is a reality. Multi-cloud is an option." Name which multi-cloud type you mean and why.
  Cloud-agnostic often means lowest common denominator, so you end up running the higher-level services yourself.
- **Sources:** https://architectelevator.com/cloud/multi-cloud-decision-model/ ;
  https://www.lastweekinaws.com/blog/multi-cloud-is-the-worst-practice/

<a id="h28"></a>
### H28 Set reliability targets below 100% and agree them with stakeholders
- **Rule:** extreme reliability limits feature velocity and raises cost. Derive DR strategy from RTO and RPO,
  then choose the cheapest strategy that meets them.
- **Sources:** https://sre.google/sre-book/embracing-risk/ ; https://sre.google/workbook/implementing-slos/ ;
  https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html

---

## Open contradictions (present both sides and decide per context)

1. **Monolith-first vs start with services.** Resolve by domain certainty and team count (H7).
2. **Innovation tokens vs "measure operational overhead directly".** Use tokens as a budget prompt, and judge
   each one by its real operational cost (H8).
3. **Defer (LRM, YAGNI) vs decide up front** (in SEI's preliminary, non-random sample, missing decisions or
   investigations were the largest risk-theme class). Resolve via
   reversibility and cost of delay: defer what is cheap to defer, decide one-way doors early (H1, H4).
   https://sei.cmu.edu/documents/2902/2006_017_001_22523.pdf
4. **Analytic rigour vs expert intuition.** Allow recognition-primed decisions for two-way or urgent calls;
   require alternatives and debiasing for one-way doors (H16).
5. **Build for longevity vs sacrificial architecture.** Both sides agree on internal modularity; they disagree
   on up-front investment. Let phase (H15) and reversibility (H1) decide.
   https://web.stanford.edu/~ouster/cgi-bin/cs190-winter18/lecture.php?topic=complexity
6. **"Boring" vs "state of the art".** Spend a token only where a requirement provably needs it, shown by napkin
   maths or a spike. This is how the owner's "state-of-the-art" and "as simple as possible" goals coexist.

<a id="sources"></a>
## Sources

All links are inline above. Debiasing evidence for option generation: Borowa et al., ICSA 2025,
https://arxiv.org/abs/2502.04011. One experiment with 16 students and 20 practitioners, from a single research
group; it found its practitioner group more susceptible to anchoring than its students. Treat it as a reason
to run the checks, not as a population estimate.
