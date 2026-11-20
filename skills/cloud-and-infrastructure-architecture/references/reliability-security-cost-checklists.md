# Review question bank

A vendor-neutral review in the Well-Architected style. Run it as a conversation about trade-offs, not as an audit. "The process for reviewing an architecture is a constructive conversation about architectural decisions, and is not an audit mechanism." ([AWS](https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html))

Frameworks don't agree on their pillar lists, and their structure changes. Treat the six headings below as lenses, not as a canonical list. ([AWS](https://docs.aws.amazon.com/wellarchitected/latest/framework/framework.html), [Azure](https://learn.microsoft.com/en-us/azure/well-architected/pillars), [Google](https://cloud.google.com/architecture/framework))

**How to use it**
1. Review foundations before the workload: identity, account or project structure, central logging, inventory, networking and cost management. If these are weak, every workload built on them inherits the weakness. Provider frameworks restructure over time; read the current release notes to see how the framework currently separates foundations from workloads. ([AWS release notes](https://docs.aws.amazon.com/wellarchitected/latest/framework/release-notes.html), [Foundations guide](decision-guides.md#5-foundations-before-workloads))
2. Answer each question with Yes, Partial, No or N/A, plus the evidence (a link, a command output, a drill record). For anything that isn't a Yes, record the risk and a proposed improvement.
3. Order improvements by risk multiplied by how cheap they are to fix. Save the result as a dated milestone, and re-review on a cadence (Azure suggests, for example, every four months) or after any major change. ([Azure](https://learn.microsoft.com/en-us/azure/well-architected/design-guides/implementing-recommendations))
4. Add a custom lens for the owner's stated priorities, the way review tools support custom lenses ([AWS lenses](https://docs.aws.amazon.com/wellarchitected/latest/userguide/lenses.html)). These are constraints from the framing: price what each costs, and where one conflicts with a requirement, show the cost and ask the owner rather than overriding either. For example:
   - Was a free or self-hostable option considered first, and what would the preference cost?
   - Were operator hours priced?
   - Was the official or supported option considered first?
5. Scale the review to the system. A solo service needs at least the questions marked ★; the security level questions follow risk, not size.

## Foundations and network

- ★ Are production and non-production separated by an account, project or subscription boundary, with a named owner for each? ([Foundations guide](decision-guides.md#5-foundations-before-workloads))
- ★ Is there one identity source for humans, with MFA, and federated short-lived identity for workloads?
- Are resources tagged for owner, environment, data class and cost centre, and is the convention enforced in IaC?
- ★ Are audit logs and backups kept where production credentials can't delete them?
- Who owns DNS and the registrar account, and is that login protected like production?
- Is the address plan free of collisions with likely peering or VPN ranges?
- Is outbound traffic from sensitive workloads controlled, and are data stores and admin APIs reachable only privately (or the exception documented)?
- ★ Who rebuilds images and ships a critical patch, and how fast? ([CISA #StopRansomware guide](https://www.cisa.gov/resources-tools/resources/stopransomware-guide))
- Could you investigate an incident: which logs, kept how long, tamper-resistant, with an incident lead named? ([NIST SP 800-61r3](https://csrc.nist.gov/pubs/sp/800/61/r3/final))

## Operations

- ★ Is every piece of infrastructure declared in version control and applied by automation, with no hand-made drift? ([OpenGitOps](https://opengitops.dev/))
- ★ Can one person deploy and roll back by following a written runbook?
- ★ Is toil counted (manual, repetitive, grows with scale), and is there a plan to cut the largest item? ([SRE toil](https://sre.google/sre-book/eliminating-toil/))
- ★ Which components does the operator personally have to back up, patch and debug? Is that list as short as the requirements allow? ([solo playbook](https://imad.website/writing/solo-developer-playbook/))
- Are delivery throughput and instability measured, including how long it takes to recover from a failed deployment? ([DORA](https://dora.dev/guides/dora-metrics/))
- If a platform exists, is it the thinnest viable layer, run as a product with real users? Is its value measured by adoption, task success and time, cognitive load, reliability, support burden and cost alongside DORA, with a before/after baseline for any causal claim? ([CNCF](https://tag-app-delivery.cncf.io/whitepapers/platforms/), [platform measures](decision-guides.md#9-platform-engineering))
- How many innovation tokens has this stack spent, and on what? ([McKinley](https://mcfunley.com/choose-boring-technology))

## Reliability

- ★ Are SLOs defined from what users experience, set below 100%, and approved by whoever owns the business risk? ([Jones & Murphy](https://www.usenix.org/conference/srecon16/program/presentation/jones), [SRE workbook](https://sre.google/workbook/implementing-slos/))
- Is there an error-budget policy saying what happens when the budget runs out? ([SRE workbook](https://sre.google/workbook/error-budget-policy/))
- Is the internal SLO tighter than the published one, without overachieving to the point that others form hidden dependencies? ([SRE book](https://sre.google/sre-book/service-level-objectives/))
- ★ Does every data class have an RTO and RPO, and does a scenario map link each in-scope failure to detection, recovery path, achieved RTO/RPO and drill evidence? ([scenario map](decision-guides.md#4d-scenario-map))
- ★ Were backup/cyber recovery, local HA and regional DR decided separately, with nobody counting replicas or HA as backups? ([data protection](decision-guides.md#4-data-protection))
- ★ Do backups meet the 3-2-1 outcome (independent copies in independent failure domains), under credentials separate from production? ([Backblaze](https://www.backblaze.com/blog/the-3-2-1-backup-strategy/))
- Where the threat model includes ransomware or a compromised admin, is at least one copy immutable or offline? ([CISA #StopRansomware guide](https://www.cisa.gov/resources-tools/resources/stopransomware-guide))
- Is retention long enough to reach before an undetected compromise or corruption began, and short enough for deletion duties? Are integrity checks automated? ([CISA](https://www.cisa.gov/audiences/small-and-medium-businesses/secure-your-business/back-up-business-data), [Google SRE data integrity](https://sre.google/sre-book/data-integrity/))
- ★ When was the last full restore into a clean, isolated environment, and is failback written down and tested?
- Multi-zone redundancy and static stability checked separately: does a one-zone-removed load test pass, and does recovery work with the control plane impaired? ([static stability](https://aws.amazon.com/builders-library/static-stability-using-availability-zones/), [fault isolation](https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/static-stability.html))
- Before going multi-region, has single-region resilience been exhausted? If the availability target is high, is recovery automated rather than manual? ([Multi-Region fundamentals](https://docs.aws.amazon.com/prescriptive-guidance/latest/aws-multi-region-fundamentals/fundamental-1.html))
- At large scale, is blast radius limited, for example with cells? ([cells](https://docs.aws.amazon.com/wellarchitected/latest/reducing-scope-of-impact-with-cell-based-architecture/what-is-a-cell-based-architecture.html))
- Is capacity forecast from users, request rate, seasonality, events and growth, rather than guessed? ([Google](https://cloud.google.com/architecture/infra-reliability-guide/traffic-load))

## Security

- ★ Has a threat model answered the four questions, with STRIDE prompting "what can go wrong", and has it been revisited since the last major change? ([4QuestionFrame](https://github.com/adamshostack/4QuestionFrame), [STRIDE](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats), [Manifesto](https://threatmodelingmanifesto.org))
- ★ Is MFA enforced for every human? Do workloads use federated, short-lived credentials instead of static keys? ([IAM best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html))
- Are permissions least-privilege, built from observed access, and are unused credentials removed? (same source)
- ★ Are secrets kept out of repos and stored centrally, with rotation that's automated where possible? ([OWASP secrets](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html))
- Is access decided by identity and device rather than network location? ([NIST 800-207](https://csrc.nist.gov/pubs/sp/800/207/final), [CISA ZTMM](https://www.cisa.gov/resources-tools/resources/zero-trust-maturity-model))
- ★ Is the inbound attack surface as small as possible?
- Was the ASVS level chosen from application risk, data sensitivity and user expectations (not team size), and is organisational maturity known where a programme requires it (SAMM)? ([ASVS 5.0](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x03-What-is-the-ASVS.md), [SAMM](https://owaspsamm.org/))
- Supply chain: was the SLSA build level chosen from the supply-chain threat model and release criticality? Are artefacts signed? Is dependency health checked? ([SLSA](https://slsa.dev/spec/v1.0/levels), [Sigstore](https://docs.sigstore.dev/cosign/signing/overview), [Scorecard](https://github.com/ossf/scorecard), [SSDF](https://csrc.nist.gov/pubs/sp/800/218/final))
- Is the shared-responsibility boundary written down for each managed service? ([AWS shared responsibility](https://aws.amazon.com/compliance/shared-responsibility-model/))
- Were compliance and data residency treated as design inputs from the start (data minimisation, purpose limitation)? ([ICO DPbD](https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-resources/accountability-and-governance/guide-to-accountability-and-governance/data-protection-by-design-and-by-default/))

## Cost

- ★ Is cost stated as a requirement and tracked per unit of business value (per user, request, tenant or job)? ([Frugal Architect](https://thefrugalarchitect.com/), [FinOps unit economics](https://www.finops.org/framework/capabilities/unit-economics))
- ★ Is cost data timely enough to act on before month-end, and are there budget alerts or controls? ([FinOps principles](https://www.finops.org/framework/principles/))
- Do infrastructure changes show their cost difference before merge? ([Infracost](https://github.com/infracost/infracost-gh-action))
- Are data transfer, cross-zone and NAT charges modelled? ([AWS blog](https://aws.amazon.com/blogs/architecture/overview-of-data-transfer-costs-for-common-architectures/))
- ★ Does the compute model match the load shape? Check for serverless running at a steady high duty cycle, or idle reserved capacity behind spiky load. ([InfoQ](https://www.infoq.com/news/2023/05/prime-ec2-ecs-saves-costs), [DHH 2022](https://world.hey.com/dhh/why-we-re-leaving-the-cloud-654b47e0))
- Is lock-in priced as a switching cost, rather than either ignored or eliminated at any price? ([Hohpe](https://martinfowler.com/articles/oss-lockin.html))
- ★ Are operator hours counted next to the bill when choosing between self-hosted and managed? ([SRE toil](https://sre.google/sre-book/eliminating-toil/))
- Are steady-state workloads re-examined periodically, since cloud is an innovation premium? ([Quinn 2025](https://www.lastweekinaws.com/blog/cloud-repatriation-is-getting-complicated/))

## Performance

- ★ Was a back-of-envelope estimate done (latency budget, throughput, data size) before any tool was chosen? ([latency numbers](https://gist.github.com/jboner/2841832))
- Is the compute abstraction the least-control option that still fits the protocol and OS needs? ([Azure tree](https://learn.microsoft.com/en-us/azure/architecture/guide/technology-choices/compute-decision-tree))
- Are cacheable responses behind a shared cache, with deliberate cache keys? ([MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Caching))
- Is chatty traffic kept close together (same zone or same host) where HA allows?

## Sustainability

- Is utilisation high, with demand peaks flattened (e.g. batch work deferred)? ([AWS sustainability](https://docs.aws.amazon.com/wellarchitected/latest/framework/sustainability.html))
- Is the lightest adequate computational approach used?
- Where it matters, is carbon measured per functional unit (SCI) rather than in total? ([SCI](https://sci.greensoftware.foundation/))
- Is it clear which sustainability levers sit with the provider and which with you? ([shared responsibility](https://docs.aws.amazon.com/wellarchitected/latest/sustainability-pillar/the-shared-responsibility-model.html))

## Output of a review

Produce a dated table with these columns: lens | question | answer | evidence | risk (H/M/L) | improvement | owner | re-check date. Turn the High risks into ADRs or tickets (see `architecture-decision-making`).
