# Decision guides

Each guide lists options, the forces that favour each, when not to use it, and sources. Drop options that fail a hard force, then choose among the rest by the stated selection rule. Record each choice and the rejected options in an ADR (see `architecture-decision-making`). Cost figures quoted below are historical and specific to their case; they show how cost curves behave, not current prices.

## 1. Compute option matrix

Every major vendor's decision tree starts with how much control you need against how much operating burden you can carry. Sources: [Azure compute tree](https://learn.microsoft.com/en-us/azure/architecture/guide/technology-choices/compute-decision-tree), [AWS compute guide](https://docs.aws.amazon.com/decision-guides/latest/decision-guides/choosing-aws-compute-service.html), [AWS container guide](https://docs.aws.amazon.com/decision-guides/latest/decision-guides/choosing-aws-container-service.html), [Google compute options](https://cloud.google.com/blog/topics/developers-practitioners/where-should-i-run-my-stuff-choosing-google-cloud-compute-option).

The options are not rungs. They differ on several axes at once, so no single order holds: a managed function can leave less to run than one container host, and a PaaS is a managed runtime, not a single box.

**Selection rule.**
1. Write each force below as a requirement, marking which are hard.
2. Score each candidate, including the current setup, against every force: meets, meets with work (say what work), fails.
3. Drop candidates that fail a hard force.
4. Of the rest, pick the one that leaves the operator the least to run, patch and debug ("least control that meets the forces"). Break ties on cost at the forecast load, exit path and team familiarity.
5. Verify the pick against this situation: current quota, region-availability and pricing pages at forecast load, and a trial deploy of the real workload where cheap; if it fails, return to step 3.
6. Record the force that would flip the choice, and re-score when it changes.

**Forces.**

| Force | Question to answer | Pushes toward more control when |
|---|---|---|
| Runtime and protocol control | Which protocols, OS features, kernel modules, GPUs or licensed software are needed? | Non-HTTP protocols, custom kernels, licence-bound hosts |
| State | Where does state live, and how large are handoffs between steps? | Local disk, long-lived connections, heavy inter-step state |
| Duty cycle and load shape | Steady, spiky, seasonal or unknown; what share of time is the work busy? | High, steady utilisation (pay-per-use loses its edge) |
| Scaling lag | How fast must capacity follow demand, and what does a slow scale-up cost? | Pre-warmed capacity needed; scale-from-zero unacceptable |
| Tenancy and isolation | Shared kernel acceptable? Regulatory or customer isolation demands? | Dedicated hosts, hard multi-tenant isolation |
| Portability | What is the exit path, and what does switching cost? | A priced need to move between providers or on-premises |
| Cold start and latency | Tail-latency budget for the first request after idle? | Tight p99 budgets on infrequently hit paths |
| Execution limits | Maximum run time, memory, payload, concurrency per unit | Long jobs, large payloads, high fan-out beyond provider limits (check current quota pages) |
| Operator capacity | Hours per week the team can spend running this | (Pushes the other way: less capacity favours managed options) |

**Option profiles.**

| Option | What you run | Often fits | When not | Sources |
|---|---|---|---|---|
| Managed functions | Code and configuration | Event-driven, bursty, low duty cycle; work inside execution limits | Steady high throughput; heavy state handoffs between steps. One monitoring pipeline cut cost by about 90% after consolidating functions plus orchestration plus object-store intermediates onto containers | [AWS cost principles](https://docs.aws.amazon.com/wellarchitected/latest/cost-optimization-pillar/design-principles.html), [InfoQ 2023](https://www.infoq.com/news/2023/05/prime-ec2-ecs-saves-costs) |
| PaaS / managed containers | Images or code, scaling settings | HTTP workloads; early or irregular load; no ops staff. Cloud is strong at "two ends of the spectrum": tiny or early apps and highly irregular load | Non-HTTP protocols or OS-level control; bills that climb steeply at steady scale | [DHH 2022](https://world.hey.com/dhh/why-we-re-leaving-the-cloud-654b47e0), [Google](https://cloud.google.com/blog/topics/developers-practitioners/where-should-i-run-my-stuff-choosing-google-cloud-compute-option) |
| Containers on one or a few hosts (service manager or Compose) | Hosts, OS patching, deploys | Steady load; one region; RTO of hours acceptable; zero-downtime container deploys to plain servers are possible | HA with no single point of failure; order-of-magnitude swings; hard multi-tenant isolation. Offset the single point of failure with tested restores | [Kamal](https://kamal-deploy.org/) |
| VMs (IaaS) | OS and everything above | Lift-and-shift, full OS control, licensed software | Hand-patching a fleet with no automation (toil) | [Azure tree](https://learn.microsoft.com/en-us/azure/architecture/guide/technology-choices/compute-decision-tree), [SRE toil](https://sre.google/sre-book/eliminating-toil/) |
| Managed Kubernetes | Cluster configuration, add-ons, upgrades, workloads | Many services or teams needing a shared deploy API; a team that owns the platform; a need for operators and reconcilers | Few services and a small team; adoption "to avoid lock-in", which swaps one lock-in for complexity | [arp242](https://www.arp242.net/dont-need-k8s.html), [k8s.af](https://k8s.af/), [Hohpe](https://martinfowler.com/articles/oss-lockin.html) |
| Own hardware / colo | Hardware, network, power contracts, everything above | Steady, predictable load at medium or larger scale; existing ops skill; long hardware life. One company reported large multi-year savings "without changing the size of the operations team" | Uncertain growth (the same company's cloud absorbed a signup surge about 10× its forecast); no ops staff; unacceptable hardware lead times. An analyst calls broad repatriation a "myth" | [DHH 2023](https://world.hey.com/dhh/we-have-left-the-cloud-251760fb), [Ars Technica 2024](https://arstechnica.com/information-technology/2024/10/basecamp-maker-37signals-says-its-cloud-exit-will-save-it-10m-over-5-years) |

Framing: moving to the cloud is an innovation play, not a way to save money; re-examine steady-state workloads on a schedule. [Quinn 2025](https://www.lastweekinaws.com/blog/cloud-repatriation-is-getting-complicated/)

## 2. Self-host vs managed

Decide per component. Score each candidate on the criteria, then apply the default and check its exceptions.

| Criterion | Question | Evidence to collect |
|---|---|---|
| SLO, RPO, RTO | Can we meet the targets ourselves, including at 3 a.m.? | Our past incident and restore times; provider SLA and its remedies |
| Residency and sovereignty | Where must data and keys live, and who may access them? | Legal requirement; provider region and access terms |
| Security boundary | Who holds admin access, keys and patch duty? | Shared-responsibility statement for the service |
| Exit path | How do we get data and configuration out, how long does it take, and what does it cost? | Export formats, egress price, a trial export |
| Support | Who answers when it breaks, and how fast? | Support tier, community health, vendor viability |
| Failure visibility | Will we see the failure and its cause, or only a status page? | Metrics and logs exposed; provider postmortem history |
| Restore ownership | Who performs and tests the restore? Does the provider's backup survive account compromise or closure? | Restore runbook; a dated drill |
| Unit economics | Cost per unit of value at forecast load, including operator hours | Pricing calculator; toil estimate |
| Team skill | Has someone on the team run this in production? | Named person; their backup |

**Small-team default:** self-host a component when it is boring and stable, has an official maintained package, has scripted and tested restores, and has bounded toil; otherwise use a managed service. **Exceptions:** residency, sovereignty or unit economics at steady scale can favour self-hosting a component that misses one of these tests; a single critical stateful component whose HA failure modes you can't rehearse can favour managed even when it passes them. Watch for one person becoming "the backup" for too many parts.

**Owner-preference lens.** If the owner prefers free, self-hostable or official tools, consider those first and show what the preference costs in operator hours and risk. Treat it as a constraint from the framing: where it conflicts with a criterion, show the cost and ask the owner rather than overriding either.

| Option | Forces | When not | Sources |
|---|---|---|---|
| Self-host | Passes the default tests; sovereignty favours it | Untestable restores; unbounded toil | [SRE toil](https://sre.google/sre-book/eliminating-toil/), [solo playbook](https://imad.website/writing/solo-developer-playbook/), [Quinn 2025](https://www.lastweekinaws.com/blog/cloud-repatriation-is-getting-complicated/) |
| Managed open-source (the same software run by a provider) | Buy the operations, keep a low switching cost | Proprietary extensions you then depend on | [Hohpe](https://martinfowler.com/articles/oss-lockin.html) |
| Proprietary managed | High unique utility; switching cost acceptable once priced | Low utility and hard-to-export data | [Hohpe](https://martinfowler.com/articles/oss-lockin.html) |

Lock-in method: plot each option on switching cost against unique utility; the switching cost is the option's strike price. "Complexity can be the biggest price you pay for reducing lock-in." [Hohpe](https://martinfowler.com/articles/oss-lockin.html). Low-tier anecdote of teams returning to managed services after burning out on day-2 operations: [vendor-adjacent blog, 2026](https://bex.co/blog/2026/09/24/self-host-to-managed-boomerang-day-2-ops).

## 3. Single-cloud vs multi-cloud vs hybrid

| Option | Forces | When not | Sources |
|---|---|---|---|
| Single primary provider + portable data + IaC (default) | Use native higher-order services where their unique utility is high | A named regulatory or residency constraint rules it out | [Hohpe](https://martinfowler.com/articles/oss-lockin.html) |
| Multi-cloud: segmented, choice, parallel or portable | A named driver: regulation, acquisition, best-of-breed segmentation, negotiation leverage. Each type costs differently | "For resilience" or "to avoid lock-in" with no price. Cloud-agnostic design means the lowest common denominator: load balancers, databases, IAM and eventing become yours to run | [Hohpe model](https://architectelevator.com/cloud/multi-cloud-decision-model/), [Quinn 2020](https://www.lastweekinaws.com/blog/multi-cloud-is-the-worst-practice/) |
| Hybrid | It already exists: "Hybrid is a Reality. Multi-cloud is an option." | Creating it on purpose with no driver | [Hohpe](https://architectelevator.com/cloud/hybrid-multi-cloud/) |

## 4. Data protection

Three orthogonal decisions. Make each one; none replaces another. Replication and HA copy deletions, corruption and encryption by ransomware as faithfully as good writes, so they are not backups. Multi-region topology does not protect against a compromised admin either.

### 4a. Backup and cyber recovery

Required for every data class that can't be regenerated within its RPO.

| Property | Decide | Sources |
|---|---|---|
| Baseline outcome | At least three copies on two kinds of storage with one off-site (3-2-1), read as "independent copies in independent failure domains". Equivalent independently administered copies count. Backblaze calls 3-2-1 "a starting point rather than a complete data backup solution" and describes 3-2-1-1-0 and 4-3-2 variants for ransomware risk | [Backblaze 3-2-1](https://www.backblaze.com/blog/the-3-2-1-backup-strategy/) |
| Independent administration | Backups are written and deleted under credentials, and ideally an account, separate from production, so one compromised identity can't destroy both. Account boundaries are the usual isolation unit | [Organizing AWS environments](https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/organizing-your-aws-environment.html) |
| Immutability or offline copy | When the threat model includes ransomware, a malicious insider or a compromised admin: immutable (object-lock style) storage or an offline copy. CISA: "Maintain offline, encrypted backups of critical data" and keep "golden images" of critical systems | [CISA #StopRansomware guide](https://www.cisa.gov/resources-tools/resources/stopransomware-guide), [CISA small business](https://www.cisa.gov/audiences/small-and-medium-businesses/secure-your-business/back-up-business-data) |
| Retention and deletion | Retention per data class, long enough to reach before an undetected compromise or corruption began (CISA suggests being able to roll back at least seven days), and short enough to honour deletion and minimisation duties | [CISA small business](https://www.cisa.gov/audiences/small-and-medium-businesses/secure-your-business/back-up-business-data), [ICO DPbD](https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-resources/accountability-and-governance/guide-to-accountability-and-governance/data-protection-by-design-and-by-default/) |
| Integrity checks | Automated verification that a backup restores and its data is consistent (checksums, a test restore, application-level checks). Independent backups of related stores complicate consistent recovery; plan for it | [Google SRE data integrity](https://sre.google/sre-book/data-integrity/) |
| Clean-room restore | Restore into an isolated environment with fresh credentials and patched images, so you don't restore the attacker or the bug along with the data | [CISA #StopRansomware guide](https://www.cisa.gov/resources-tools/resources/stopransomware-guide) |
| Failback | How you return to the primary after recovery, and how data written meanwhile is reconciled | (practitioner judgement; test it in the same drill as failover) |
| Drills | Scheduled full and partial restores against the scenario map, with times recorded against the RTO/RPO. CISA: scheduled recovery tests verify integrity and refine RPOs and RTOs | [CISA small business](https://www.cisa.gov/audiences/small-and-medium-businesses/secure-your-business/back-up-business-data) |

When not: caches and derived data that you can rebuild within the RPO; make the rebuild the tested path instead.

### 4b. Local high availability

Two separate choices; verify each on its own.

| Choice | Forces | When not | Sources |
|---|---|---|---|
| Multi-zone redundancy (topology) | The SLO can't tolerate a single host or zone failure | Hobby or one-person scale, where cross-zone transfer and extra parts outweigh the benefit; check whether one instance plus a tested restore meets the SLO (expected failure rate × measured restore time against the error budget) | [data transfer costs](https://aws.amazon.com/blogs/architecture/overview-of-data-transfer-costs-for-common-architectures/) |
| Static stability (capacity policy) | Recovery must not need new capacity or a working control plane: capacity for the load is already provisioned in the surviving zones | Tiers where waiting for scale-up during a zone event is acceptable | [Static stability](https://aws.amazon.com/builders-library/static-stability-using-availability-zones/), [fault isolation](https://docs.aws.amazon.com/whitepapers/latest/aws-fault-isolation-boundaries/static-stability.html) |

Verify separately: remaining-zone capacity under peak load (a load test with one zone removed), and behaviour with the control plane impaired (can traffic shift and instances keep serving with no API calls?).

### 4c. Regional DR

Only when a regional scenario is in scope and single-region resilience can't meet its RTO/RPO, or residency demands it.

| Option | Forces | When not | Sources |
|---|---|---|---|
| Backup and restore into another region | Regional loss in scope; RTO of hours | The regional RTO is minutes | [AWS DR options](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html) |
| Pilot light / warm standby | RTO of minutes to hours for failover | Single-region resilience not yet exhausted | [AWS DR options](https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html) |
| Active/active | Only the top tier (for example RTO about 15 minutes, RPO about 5 minutes in AWS's example). Data replication is the hard part | Any goal one region can meet | [Multi-Region fundamentals](https://docs.aws.amazon.com/prescriptive-guidance/latest/aws-multi-region-fundamentals/fundamental-1.html) |

Arithmetic: 99.99% availability allows about 52.6 minutes of downtime a year, and detecting an outage alone can take 5 minutes or more, so four nines needs automated recovery. [Multi-Region fundamentals](https://docs.aws.amazon.com/prescriptive-guidance/latest/aws-multi-region-fundamentals/fundamental-1.html). At large scale, cells limit blast radius: [cell-based architecture](https://docs.aws.amazon.com/wellarchitected/latest/reducing-scope-of-impact-with-cell-based-architecture/what-is-a-cell-based-architecture.html).

### 4d. Scenario map

Fill one row per scenario in scope. Run the drill where it is safe and cheap. Otherwise state that scenario's RTO/RPO as unverified, say what blocked the drill, keep any choice from resting on it, and schedule the drill with an owner.

| Scenario | Detection | Recovery path | Target and achieved RTO/RPO | Drill evidence |
|---|---|---|---|---|
| Accidental deletion or bad migration | Alerts on row-count or error-rate change; user report | Point-in-time or backup restore (4a) | e.g. target RPO 1 h; achieved from the last drill | Dated restore log |
| Ransomware or compromised admin credentials | Unusual deletion, encryption or IAM activity; immutable audit log | Clean-room restore from immutable/offline copy (4a); rotate credentials | | Dated clean-room rebuild |
| Silent corruption replicated to all copies | Integrity checks; application validation | Restore from a copy older than the corruption (retention, 4a) | | Integrity-check history |
| Host or instance failure | Health checks | Restart or replace; multi-zone failover if present (4b) | | Failure-injection record |
| Zone failure | Zone-level health signals | Surviving zones absorb load (4b, static stability) | | One-zone-removed load test |
| Control-plane impairment | Provider status; failed API calls | Pre-provisioned capacity; no control-plane dependency (4b) | | Game-day record |
| Region loss | Regional health signals | Regional DR option (4c) or accepted downtime | | Failover drill |
| Provider account lockout or closure | Access failure; billing notices | Backups held in a separate account or provider (4a) | | Restore from the separate account |
| Third-party SaaS outage or data loss | Vendor status; sync errors | Exported copies; documented manual fallback | | Export restore test |

## 5. Foundations before workloads

Workloads inherit their foundation's strengths and gaps. Scale each item to the system.

| Item | Decide | Small-system minimum | Sources |
|---|---|---|---|
| Account, project or subscription boundaries | Which boundary separates environments, workloads, security tooling and backups | Separate production from non-production; a separate place for backups and logs | [Organizing AWS environments](https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/organizing-your-aws-environment.html), [Azure landing zone](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/), [Google resource hierarchy](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy) |
| Ownership and inheritance | Who owns each boundary; which policies and access inherit down the hierarchy | A named owner per account; break-glass access documented | [Google resource hierarchy](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy) |
| Central identity | One identity source for humans; federated, short-lived workload identity | MFA for every human; no shared root use | [IAM best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) |
| Inventory and tagging | Tags for owner, environment, data class, cost centre; enforced at creation | A tag convention checked in IaC | [FinOps principles](https://www.finops.org/framework/principles/) |
| Central logs and billing | Where audit logs and cost data land, and who can delete them | Audit logs exported to the backup/log account; budget alerts | [AWS release notes](https://docs.aws.amazon.com/wellarchitected/latest/framework/release-notes.html) |

Provider landing-zone guidance changes; read the current version rather than citing a structure from memory.

## 6. Network, trust boundaries and edge

| Decision | Forces / what to decide | When not | Sources |
|---|---|---|---|
| Trust boundaries | Where identity is re-checked; no implicit trust from network location | — | [NIST SP 800-207](https://csrc.nist.gov/pubs/sp/800/207/final), [BeyondCorp](https://research.google/pubs/beyondcorp-a-new-approach-to-enterprise-security/) |
| DNS | Who owns the zones and registrar account; TTLs and failover; protecting the registrar login like production | — | (practitioner judgement) |
| Address plan | Ranges that won't collide with future peering, VPNs or acquisitions | A single host with no private networking | (practitioner judgement) |
| Inbound exposure | Only what must be public; admin planes behind identity-aware access or a VPN | — | [NIST SP 800-207](https://csrc.nist.gov/pubs/sp/800/207/final), [BeyondCorp](https://research.google/pubs/beyondcorp-a-new-approach-to-enterprise-security/) |
| Egress control | Allow-lists or proxies for outbound traffic from sensitive workloads; NAT priced | Low-sensitivity workloads where the cost outweighs the risk | [data transfer costs](https://aws.amazon.com/blogs/architecture/overview-of-data-transfer-costs-for-common-architectures/) |
| Private connectivity | Data stores and admin APIs reachable only privately | Managed services that only offer public endpoints with strong identity; document the exception | (practitioner judgement) |
| Patch and image lifecycle | Who rebuilds images, how often, and how fast a critical fix ships; immutable images over in-place patching where possible; keep golden images | — | [CISA #StopRansomware guide](https://www.cisa.gov/resources-tools/resources/stopransomware-guide), [SRE toil](https://sre.google/sre-book/eliminating-toil/) |
| Forensic readiness | Which logs, how long, stored where an attacker with production access can't alter them; clock sync; who leads an incident | — | [NIST SP 800-61r3](https://csrc.nist.gov/pubs/sp/800/61/r3/final) |
| Shared cache / CDN | Latency and origin load; deliberate `Cache-Control` and `Vary` | Highly personalised responses with no cache-key strategy | [MDN caching](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Caching) |
| Same-zone placement for chatty traffic | Same-zone traffic is free; cross-zone and NAT processing are charged per GB | HA needs spreading across zones; price the transfer instead | [data transfer costs](https://aws.amazon.com/blogs/architecture/overview-of-data-transfer-costs-for-common-architectures/) |
| Edge compute | Adds another runtime and deploy target; use when measured user latency from the regions served misses the target and a CDN or region change does not fix it | Latency targets already met from the current region and cache | (practitioner judgement) |

## 7. Security assurance, chosen by risk

**Baseline for any internet-facing system:** a four-question threat model; MFA everywhere; secrets out of git (for example SOPS) or in a secrets manager; automated updates; least-privilege IAM with temporary credentials; dependency health checks; backups per 4a. Sources: [4QuestionFrame](https://github.com/adamshostack/4QuestionFrame), [SOPS guide](https://fluxcd.io/flux/guides/mozilla-sops/), [IAM best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html), [Scorecard](https://github.com/ossf/scorecard).

| Decision | Choose from | Not from | Sources |
|---|---|---|---|
| Application assurance (ASVS level) | Application risk, data sensitivity, user expectations, risk reduction against implementation effort. ASVS 5.0: "Most applications should be striving" for Level 2; it suggests starting a tailored version from Level 1 and advancing by risk, and gives a limited-data startup (L1) and online banking (L3) as contrasting examples | Headcount or company size | [ASVS 5.0: What is the ASVS](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x03-What-is-the-ASVS.md), [ASVS 5.0 for 4.0 users](https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x05-For-Users-Of-4.0.md) |
| Supply-chain build level (SLSA) | The supply-chain threat model and release criticality: L1 provenance exists; L2 signed provenance from a hosted build platform; L3 a hardened build platform, which the spec says is intended for "most software releases" | Headcount or company size | [SLSA levels](https://slsa.dev/spec/v1.0/levels), [Sigstore](https://docs.sigstore.dev/cosign/signing/overview) |
| Programme frameworks (SAMM, SSDF, zero-trust maturity, compliance mapping) | A customer, regulator or organisational programme requires them, or several teams need a shared maturity yardstick | A single system with no such obligation | [SAMM](https://owaspsamm.org/), [SSDF](https://csrc.nist.gov/pubs/sp/800/218/final), [CISA ZTMM](https://www.cisa.gov/resources-tools/resources/zero-trust-maturity-model), [ICO DPbD](https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-resources/accountability-and-governance/guide-to-accountability-and-governance/data-protection-by-design-and-by-default/) |

**Operator-capacity lens.** Team size changes how you reach a level, not which level the risk demands. A small team usually reaches a build level cheapest through a hosted CI platform and keyless signing rather than a self-run build farm, and reaches ASVS requirements through framework defaults and managed identity. If the required level is out of reach for the team, say so, and treat it as a risk to accept, fund or design around.

Use STRIDE to prompt "what can go wrong": [Microsoft](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats). For secrets, centralise them, automate rotation and prefer dynamic secrets: [OWASP](https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html).

## 8. IaC and GitOps (choose the category, then the tool)

GitOps is four principles: declarative, versioned and immutable, pulled automatically, and continuously reconciled. [OpenGitOps](https://opengitops.dev/)

| Category | Examples (foundation-backed first) | Forces / when not | Sources |
|---|---|---|---|
| Declarative provisioning | OpenTofu (a Linux Foundation fork made after the licence change); Terraform | Broad provider ecosystem. Check the licence if you're building a competing product | [LF announcement](https://www.linuxfoundation.org/press/announcing-opentofu), [OpenTofu FAQ](https://opentofu.org/faq/) |
| General-purpose-language IaC | Pulumi | Suits code-first teams. Watch for over-abstracted infrastructure code | [Pulumi concepts](https://www.pulumi.com/docs/iac/concepts/) |
| Control-plane IaC | Crossplane | Only if you already run Kubernetes and want self-service APIs | [Crossplane](https://www.crossplane.io/), [CNCF graduation](https://www.cncf.io/announcements/2025/11/06/cloud-native-computing-foundation-announces-graduation-of-crossplane) |
| Reconcilers | Argo CD, Flux | Built around Kubernetes. On a single box, CI-driven deploys are simpler | [Argo CD](https://argo-cd.readthedocs.io/en/stable/), [Flux](https://fluxcd.io/) |
| Encrypted secrets in git | SOPS + age | Not a replacement for a secrets manager when you need rotation or dynamic secrets | [Flux SOPS guide](https://fluxcd.io/flux/guides/mozilla-sops/) |
| Secrets manager | OpenBao; cloud-native managers | Running one is an ops burden of its own: HA, unseal, backups | [OpenBao](https://openbao.org/) |
| Cost in review | Infracost; OpenCost for Kubernetes cost allocation | — | [Infracost action](https://github.com/infracost/infracost-gh-action), [OpenCost](https://www.opencost.io/) |

## 9. Platform engineering

| Option | Forces | When not | Sources |
|---|---|---|---|
| README + scripts + IaC repo | One person or one team | Several teams keep solving the same infra problems | [CNCF platforms](https://tag-app-delivery.cncf.io/whitepapers/platforms/) |
| Thinnest viable platform (docs, templates, golden paths), run as a product | Cognitive load is the bottleneck | Built big-bang with no users | [Team Topologies](https://teamtopologies.com/key-concepts), [CNCF platforms](https://tag-app-delivery.cncf.io/whitepapers/platforms/) |

**Measuring platform value.** Track a small set from each group, with a baseline taken before the platform change:

| Group | Example measures |
|---|---|
| Adoption | Active users, retention and churn, share of services on the golden path |
| Task success and time | Time to first deploy, time to provision an environment, task completion without help |
| Cognitive load and satisfaction | Short user surveys; the CNCF paper points to the SPACE framework for productivity measures |
| Reliability | Incidents and SLO attainment for platform capabilities |
| Support burden | Tickets and requests per user; manual steps removed |
| Cost | Platform cost per team or service; infrastructure cost per unit |
| Delivery (DORA) | Throughput and instability, applied per service, not compared across unlike services |

Sources: [CNCF platforms](https://tag-app-delivery.cncf.io/whitepapers/platforms/) (user satisfaction and productivity, organisational efficiency, product and feature delivery), [DORA](https://dora.dev/guides/dora-metrics/), [CNCF maturity model](https://www.cncf.io/blog/2023/11/20/announcing-the-platform-engineering-maturity-model).

Attribution: DORA metrics moving after a platform launch don't show the platform caused it. Claim an effect only with a before/after comparison, ideally against teams not yet onboarded, and name other changes in the same window.

## 10. Cost and sustainability

| Lever | Method | Sources |
|---|---|---|
| Cost as a non-functional requirement | The seven laws of the Frugal Architect, including "unobserved systems lead to unknown costs" | [Frugal Architect](https://thefrugalarchitect.com/) |
| Unit economics | Cost per unit of business value, not aggregate spend; break-even from marginal cost against marginal revenue | [FinOps principles](https://www.finops.org/framework/principles/), [Unit economics](https://www.finops.org/framework/capabilities/unit-economics), [Google cost pillar](https://docs.cloud.google.com/architecture/framework/cost-optimization) |
| Capacity | Forecast from users, request rate, history, seasonality, events and growth; envelope maths first. "Stop guessing capacity" (rented) and "over-provision cheaply" (owned) are both rational under different cost curves | [Google traffic load](https://cloud.google.com/architecture/infra-reliability-guide/traffic-load), [latency numbers](https://gist.github.com/jboner/2841832), [AWS design principles](https://docs.aws.amazon.com/wellarchitected/latest/framework/design-principles.html), [DHH 2023](https://world.hey.com/dhh/we-have-left-the-cloud-251760fb) |
| Carbon | SCI is carbon per functional unit (an ISO standard since 2024). Maximise utilisation, flatten peaks, use the lightest adequate compute | [SCI](https://sci.greensoftware.foundation/), [ISO status](https://greensoftware.foundation/articles/sci-specification-achieves-iso-standard-status/), [AWS sustainability](https://docs.aws.amazon.com/wellarchitected/latest/framework/sustainability.html) |
