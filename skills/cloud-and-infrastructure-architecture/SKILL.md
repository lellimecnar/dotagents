---
name: cloud-and-infrastructure-architecture
description: Use when choosing hosting, infra, DR, security or cloud cost. Fit options to forces; price lock-in.
metadata:
  version: "0.2.0"
  status: draft
  owner: maintainer
---

# Cloud and infrastructure architecture

Decide where and how a system runs, recovers, stays secure and stays affordable: the simplest setup that meets every stated requirement and can grow with real demand. Small teams are a common case; for them operator hours often cost more than the bill, so price them.

The general decision method (options, reversibility, ADRs, fitness functions) lives in `architecture-decision-making`; source rigour and the low/medium/high confidence rubric in `architecture-research-and-verification`. Before designing, run `use-existing-solutions-first`. To look at live systems, use `readonly-infrastructure-inspection`. For loopback-only apps, use `local-web-app-hardening`.

## When to use

- Choosing compute, hosting, region, provider, or rent/buy/self-host.
- Setting availability, backup, recovery, SLO or security targets, or reviewing a deployment against them.
- A cost surprise, a scaling wall, a ransomware worry, or a proposal for Kubernetes, multi-region or multi-cloud.

## Decision sequence

Earlier steps constrain later ones; revisit a step when a later one exposes a missing force.

1. **Collect the forces.** Load shape with back-of-envelope numbers; operator capacity in hours; data classes with RTO and RPO for each; threat model inputs (who might attack, what they want); compliance and residency; draft SLOs below 100%, approved by whoever owns the business risk. Measure or look up whatever can be checked (current load, logs, bills, live config). Record only what no available means can settle as an open question with an owner, what you tried and what would settle it; no choice rests on it (give verified branches, or ask the owner the one blocking question).
2. **Foundation before workload.** Workloads inherit these; settle them first, scaled to the system: account, project or subscription boundaries; isolation between production and everything else; central human and workload identity; inventory and tagging (owner, environment, data class, cost centre); named ownership and how policy inherits down the hierarchy; central logs and billing visibility. For a solo system that may be two accounts, one identity provider and a tag convention.
3. **Network and trust boundaries.** Draw where trust changes. Decide DNS ownership and failover; a non-colliding address plan; inbound exposure (nothing that doesn't need it); egress control; private connectivity to data stores and admin planes; the patch and image lifecycle (who rebuilds, how often, how fast for a critical fix); forensic readiness (which logs are kept, where an attacker can't delete them, for how long). Transfer, cross-zone and NAT charges are design outputs.
4. **Compute: match options to forces.** Score the candidates, including the current setup (functions, PaaS or managed containers, containers on hosts, an orchestrator, VMs, own hardware) against: runtime and protocol control, state, duty cycle and load shape, scaling lag, tenancy and isolation, portability, cold start and latency, execution limits, and operator capacity. Drop any option that fails a hard force, then pick the one that leaves you the least to run and patch, and confirm the pick on the current quota, region-availability and pricing pages at forecast load, with a trial deploy where cheap. This is not a ladder: a managed function can carry less burden than one container host. Re-score when load shape changes. Matrix: `references/decision-guides.md#1-compute-option-matrix`.
5. **Self-host or managed, per component.** Compare SLO/RPO/RTO, residency, security boundary, exit path, support, failure visibility, restore ownership, unit economics including operator hours, and team skill. Small-team default: self-host only what is boring, officially packaged, restore-tested and of bounded toil; otherwise buy managed, preferring managed open-source when exit matters. Exceptions run both ways (residency or cost can favour self-hosting). Price lock-in as a switching cost against unique value. The owner's stated preferences and sourcing rules (for example free, self-hostable or official tools) are constraints from the framing; where one costs materially more in operator hours or risk, show the cost and ask the owner before departing from it.
6. **Data protection: three separate decisions.** None substitutes for another; replicas copy corruption and deletions.
   - *Backup and cyber recovery.* Copies administered by separate credentials from production; immutable or offline where the threat model includes ransomware or a compromised admin; retention and deletion per data class; integrity checks; restore into a clean, isolated environment; a failback plan; scheduled drills. Treat 3-2-1 as the baseline outcome (independent copies in independent failure domains), not as a ritual.
   - *Local HA.* Choose multi-zone redundancy (topology) separately from static stability (pre-provisioned capacity in the surviving zones, recovery that doesn't need the control plane). Verify each on its own.
   - *Regional DR.* Only when regional loss is in scope and one region can't meet its RTO/RPO: restore elsewhere, pilot light, warm standby or active/active. Data is the hard part.
   - Map each failure scenario to detection, recovery path, achieved RTO/RPO and drill evidence. Table: `references/decision-guides.md#4-data-protection`.
7. **Security architecture.** Four-question threat model with STRIDE prompting "what can go wrong"; zero trust (no implicit trust from network location); least privilege built from observed access; short-lived, federated credentials; central secrets with rotation. Choose the application assurance level (ASVS) from application risk, data sensitivity and user expectations, and the supply-chain build level (SLSA) from the supply-chain threat model and release criticality. Team size doesn't set the level; it only shapes how you reach it, such as hosted CI rather than a self-run build farm.
8. **IaC and GitOps.** Declarative, versioned, reviewed infrastructure; category before tool. On a single host a CI-driven deploy is enough; a reconciler earns its place with an orchestrator.
9. **Observability, SLOs and platform value.** Measure what users experience; agree what happens when the error budget runs out; count toil. Judge a platform by adoption, task success and time, cognitive load, reliability, support burden and cost alongside DORA delivery metrics. Claim causation only with a before/after comparison.
10. **Cost as a requirement.** Cost per unit of business value (user, request, tenant, job); cost diff before merge; alerts and controls; small optimisation steps; the Frugal Architect laws as a lens.
11. **Sustainability.** Prefer high utilisation, flattened peaks and the lightest adequate compute. Where it matters, measure carbon per functional unit (SCI).

Review questions: `references/reliability-security-cost-checklists.md`.

## Simplicity defaults and what earns complexity

| Default | What earns the step up (name it in the ADR) |
|---|---|
| The lightest compute option that passes every hard force, one region | A force it fails: measured load, an intolerable single point of failure, isolation required by regulation |
| No orchestrator | Many services or teams needing a shared deploy API, and a team that owns the platform |
| Single region; multi-zone where the SLO needs it; independent backups always | A regional scenario whose RTO/RPO one region can't meet, or a residency rule |
| One primary provider, portable data, IaC | A named driver: regulation, acquisition, best-of-breed segmentation, negotiation leverage. Unpriced "resilience" doesn't count |
| No platform team; README, scripts, IaC repo | Several teams solving the same infra problem, with cognitive load as the bottleneck |
| Boring, well-known tools | A problem the boring tool can't solve. Spend innovation tokens one at a time |

## Principles

**Size to the operator, not to the diagram.**
- Value: a small team's attention and ability to recover.
- How to spot a breach: one person is "the backup" for everything.
- When not: teams with dedicated operations staff.

**Redundancy is not recoverability.**
- Value: survival of deletion, corruption and ransomware.
- How to spot a breach: "we have replicas" offered as the backup plan; backups deletable with production credentials.
- When not: disposable data you can regenerate within the RPO.

**A restore you haven't run is a hope.**
- Value: the stated RTO and RPO.
- How to spot a breach: no dated restore drill; drills only of single files, never a full clean-room rebuild.
- When not: caches and derived data, where rebuilding is the tested path.

**Recovery must not depend on what failed.**
- Value: availability during an outage.
- How to spot a breach: recovery needs the control plane, a console login, the compromised identity provider or new capacity.
- When not: tiers where hours of downtime are acceptable.

**Reliability targets are budgets.**
- Value: the speed/cost trade-off.
- How to spot a breach: a 100% target, or an SLO nobody approved.
- When not: life-safety systems, which need their own discipline.

**Lock-in is a priced option.**
- Value: the utility of managed services.
- How to spot a breach: abstraction layers "to stay portable" with no switching-cost estimate.
- When not: data formats and identity; keeping them portable is usually cheap, so default to it.

### Examples

- **Solo SaaS, steady load:** forces were steady low traffic, one operator, a daily RPO. Candidates: the current single VM, a PaaS app, managed containers, an orchestrator. The orchestrator failed operator capacity; the bare VM lost on patch toil; the PaaS app won on least to run, after its limits and price were confirmed on the current pages at forecast load. Around it: separate production and staging accounts; nightly backups to locked storage under separate credentials; quarterly clean-room restore; CI deploys; per-tenant cost tracked.
- **Bursty batch ingest:** forces were a low duty cycle with short spikes and per-item work well inside the function limits confirmed on the current quota page. The current always-on worker lost on idle cost and a container service on scaling lag, so functions take the intake; heavy state handoffs stay inside one service to avoid per-transition cost.

## Anti-patterns

- Kubernetes for a handful of services, or to "avoid lock-in" (it becomes complexity lock-in).
- Multi-cloud or multi-region "just in case".
- Treating HA replicas or snapshots in the same account as backups.
- Workloads built before account boundaries, identity and tagging exist.
- Serverless nano-pipelines passing heavy state between stages.
- Perimeter-only trust, long-lived static keys, secrets in repos.
- Security levels picked by headcount rather than risk.
- A big platform before it has users.
- Generalising from one cloud-exit story or one vendor's framework.

## Check current state

Don't hard-code prices, instance families, quotas, product names, versions or framework structures. Check at decision time and record the date.

| What to verify | Where |
|---|---|
| Prices and the cost of the proposed shape | The provider's pricing calculator; a cost diff on the IaC change |
| Limits, quotas, product menu, status | Provider quota pages, decision guides, changelogs, status pages |
| Framework guidance | Provider frameworks restructure over time. Read the current release notes; where the framework separates organisation-wide foundations from workload guidance, review both. Use frameworks as question banks and lenses, not section citations |
| Project maturity, licence, cost practice | Foundation project pages (a landscape is a catalogue, not an endorsement); licence FAQs; the current FinOps Framework |
| Security baselines | Current NIST zero-trust, SSDF and incident-response guidance; CISA ZTMM and ransomware guide; OWASP ASVS, SAMM and Secrets cheat sheet; the SLSA spec |
| Carbon | Provider carbon dashboards, grid-intensity data, GSF SCI guidance |

State confidence for facts, forecasts and the recommendation separately, using the rubric in `architecture-research-and-verification`.

## References

- `references/decision-guides.md`: option, force and when-not guides with sources.
- `references/reliability-security-cost-checklists.md`: review question bank.
- `references/reading-list.md`: tiered reading list.
- Related skills: `architecture-decision-making`, `architecture-research-and-verification`, `use-existing-solutions-first`, `readonly-infrastructure-inspection`, `local-web-app-hardening`.
