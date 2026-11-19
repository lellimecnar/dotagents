# Review question bank

Questions for interrogating a design: your own before you propose it, or someone else's in review. Ask the
**omission** questions first. In SEI's preliminary analysis of 18 ATAM reports (systems that came to the SEI,
"not a random sample"), 57 of 99 risk themes were risks of omission (a missing decision or investigation),
25 of commission (a problematic decision) and 18 other. Use it as a prompt to hunt for omissions, not as a
population estimate. https://sei.cmu.edu/documents/2902/2006_017_001_22523.pdf

Use the groups that fit the rigour tier. A light review might use only A, C and F.

## A. Framing and missing decisions
1. What outcome is this for, who decides, and who has to live with it?
2. Which decisions here are one-way doors, and what would reversing each one really cost?
   https://s2.q4cdn.com/299287126/files/doc_financials/annual/2015-Letter-to-Shareholders.PDF
3. What decisions have *not* been made yet, and what is each one's last responsible moment?
   https://blog.codinghorror.com/the-last-responsible-moment/
4. What is deliberately excluded (ban decisions, non-goals), and is that written down?
   https://philippe.kruchten.com/wp-content/uploads/2009/07/kruchten-2004-design-decisions.pdf ;
   https://industrialempathy.com/posts/design-docs-at-google
5. What phase is the product in (explore / expand / extract), and does the design's rigour match it?
   https://newsletter.kentbeck.com/p/canon-3x-exploreexpandextract

## B. Drivers
6. What are the top three to five qualities as *measurable scenarios*, and which is sacrificed when they
   conflict? https://people.ece.ubc.ca/matei/EECE417/BASS/ch04lev1sec3.html
7. Where are the sensitivity points and trade-off points? https://www.sei.cmu.edu/documents/629/2000_005_001_13706.pdf
8. Which quality categories were ignored, and was that deliberate? https://quality.arc42.org/standards/iso-25010
9. Is there a cost target, and is it expressed per unit of business value? https://thefrugalarchitect.com/

## C. Options and simplicity
10. Which alternatives were rejected, and why? Were keep-current, extend-existing and buy/managed among them?
    https://ozimmer.ch/practices/2020/04/27/ArchitectureDecisionMaking.html
11. What is the strongest case for the option we rejected?
12. For each component beyond the baseline (one deployable, one existing datastore, one box or managed
    platform), which named requirement earns it?
13. Which new technologies does this introduce, who operates them, and what do they replace?
    https://mcfunley.com/choose-boring-technology
14. Is anything here a presumptive capability rather than ease of change? https://martinfowler.com/bliki/Yagni.html
15. Would we choose this if it didn't look good on a CV or a job ad?
    https://portal.fis.tum.de/en/publications/resume-driven-development-a-definition-and-empirical-characteriza/

## D. Cost, operations and organisation
16. What are the recurring operator hours (patching, upgrades, backups, restores, on-call)?
    https://sre.google/sre-book/eliminating-toil/
17. What is the switching cost (strike price) of each dependency, and what does reducing it cost in
    complexity? https://martinfowler.com/articles/oss-lockin.html
18. Does the team structure support these boundaries? https://www.melconway.com/Home/Committees_Paper.html ;
    https://teamtopologies.com/key-concepts
19. Is there an operational readiness plan with SLOs? https://sre.google/sre-book/evolving-sre-engagement-model
20. What's the cross-cutting story: security (threat model), privacy, observability, backup and restore?
    https://github.com/adamshostack/4QuestionFrame ; https://industrialempathy.com/posts/design-docs-at-google

## E. Evolution and confirmation
21. What load does the forecast give at the horizon and at peak (bursts, launches, seasonality), and what
    gets rewritten if growth exceeds it?
    https://martinfowler.com/bliki/SacrificialArchitecture.html
22. What are the triggers for the next step on the evolution path?
23. Which drivers have fitness functions, and which rely on periodic review?
    https://www.infoq.com/articles/fitness-functions-architecture/
24. What is the riskiest assumption, and how will we learn early that it is wrong (spike, premortem,
    measurement)? https://codingthearchitecture.com/2012/07/11/risk_storming.html

## F. Reasoning quality (decision fallacies)
Adapted from Zimmermann's seven decision-making fallacies https://ozimmer.ch/practices/2025/09/01/ADMFallacies.html
and the Kahneman, Lovallo & Sibony checklist https://hbr.org/2011/06/the-big-idea-before-you-make-that-big-decision

25. **Blind flight:** are the quality requirements measurable, or adjectives?
26. **Following the crowd:** "big company X does it". Do their requirements match ours?
27. **Anecdotal evidence:** is one success story the main support?
28. **Blending whole and part / abstraction aversion:** does one record mix concept, technology and product
    choices that should be separate decisions?
29. **Golden hammer / silver bullet:** is this the proposer's favourite tool regardless of problem?
30. **Timelessness:** has the time dimension (phase, horizon, evolution) been ignored?
31. **AI over-confidence:** were AI-generated claims verified against live sources?
32. Has the team fallen in love with its proposal? Were there dissenting opinions?
33. Did anyone actively look for evidence that would disprove the recommendation?
34. Sunk cost: if we weren't already invested, would we choose this today?
35. Are the estimates anchored on a reference class of similar efforts, with the bad tail considered?
    https://arxiv.org/abs/1302.3642 ; https://arxiv.org/abs/1304.0265

## Lightweight vs full evaluation

Routine work: a peer-run lightweight evaluation reusing these concepts takes a couple of hours and focuses on
what changed since the last review. https://robertsicoie.com/blog/lae
High-stakes systems: a full scenario-based evaluation (QAW before architecture exists, ATAM once it does).
https://www.sei.cmu.edu/library/sei-architecture-analysis-techniques-and-when-to-use-them/
Run reviews as a constructive conversation, not an audit.
https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html

Output of a review: a prioritised list of issues (not all of them blockers), each tied to a scenario or
question above. https://cloud.google.com/blog/products/gcp/how-sres-find-the-landmines-in-a-service-cre-life-lessons

<a id="sources"></a>
## Sources
Inline above.
