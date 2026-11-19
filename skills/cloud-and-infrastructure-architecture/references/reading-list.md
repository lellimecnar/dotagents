# Reading list

The list has three tiers: **Tier 1** to read first, **Tier 2** for depth on demand, and **Tier 3** for context and counterpoints. Entries marked "live" are documents that keep changing. Re-check them before citing specifics.

## Tier 1: core method

| Title | Author | Year | Type | URL |
|---|---|---|---|---|
| AWS Well-Architected Framework (and release notes) | AWS | live (structure changes; read release notes) | Official framework | https://docs.aws.amazon.com/wellarchitected/latest/framework/framework.html · https://docs.aws.amazon.com/wellarchitected/latest/framework/release-notes.html |
| Azure Well-Architected Framework (trade-off pages per pillar) | Microsoft | live | Official framework | https://learn.microsoft.com/en-us/azure/well-architected/pillars |
| The Frugal Architect | Werner Vogels | 2023 | Laws / essays | https://thefrugalarchitect.com/ |
| FinOps Framework & Principles | FinOps Foundation | live | Framework | https://www.finops.org/framework/ |
| Don't get locked up into avoiding lock-in | Gregor Hohpe | 2019 | Article | https://martinfowler.com/articles/oss-lockin.html |
| Multi-cloud decision model | Gregor Hohpe | c. 2020 | Article | https://architectelevator.com/cloud/multi-cloud-decision-model/ |
| Choose Boring Technology | Dan McKinley | 2015 | Essay / talk | https://mcfunley.com/choose-boring-technology |
| Site Reliability Engineering & SRE Workbook (free) | Google | 2016 / 2018 | Books | https://sre.google/books/ |
| Compute decision tree | Microsoft | live | Official guide | https://learn.microsoft.com/en-us/azure/architecture/guide/technology-choices/compute-decision-tree |
| Disaster Recovery of Workloads on AWS | AWS | live | Whitepaper | https://docs.aws.amazon.com/whitepapers/latest/disaster-recovery-workloads-on-aws/disaster-recovery-options-in-the-cloud.html |
| AWS Multi-Region Fundamentals | AWS | live | Prescriptive guidance | https://docs.aws.amazon.com/prescriptive-guidance/latest/aws-multi-region-fundamentals/fundamental-1.html |
| Static stability using Availability Zones | AWS Builders' Library | c. 2020 | Article | https://aws.amazon.com/builders-library/static-stability-using-availability-zones/ |
| The 3-2-1 backup strategy (3-2-1 as a starting point; 3-2-1-1-0 and 4-3-2 variants) | Backblaze | live | Vendor guide | https://www.backblaze.com/blog/the-3-2-1-backup-strategy/ |
| #StopRansomware Guide | CISA | live | Government guide | https://www.cisa.gov/resources-tools/resources/stopransomware-guide |
| Data integrity: what you read is what you wrote (SRE book ch. 26) | Google | 2016 | Book chapter | https://sre.google/sre-book/data-integrity/ |
| 4 Question Frame for threat modeling | Adam Shostack | live | Framework | https://github.com/adamshostack/4QuestionFrame |
| NIST SP 800-207 Zero Trust Architecture | NIST | 2020 | Standard | https://csrc.nist.gov/pubs/sp/800/207/final |
| OWASP ASVS (and 5.0 "What is the ASVS" on choosing levels by risk) | OWASP | live / 2025 | Standard | https://owasp.org/www-project-application-security-verification-standard/ · https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x03-What-is-the-ASVS.md |
| SLSA security levels | OpenSSF | 2023 | Spec | https://slsa.dev/spec/v1.0/levels |
| OWASP Secrets Management Cheat Sheet | OWASP | live | Guide | https://cheatsheetseries.owasp.org/cheatsheets/Secrets_Management_Cheat_Sheet.html |
| CNCF Platforms White Paper | CNCF TAG App Delivery | 2023 | White paper | https://tag-app-delivery.cncf.io/whitepapers/platforms/ |
| Team Topologies key concepts | Skelton & Pais | 2019+ | Book / site | https://teamtopologies.com/key-concepts |
| SCI Specification | Green Software Foundation | 2024 (ISO) | Standard | https://sci.greensoftware.foundation/ |
| Latency numbers every programmer should know | Jonas Bonér (after Dean/Norvig) | 2012+ | Gist | https://gist.github.com/jboner/2841832 |
| Overview of data transfer costs for common architectures | AWS | c. 2021 | Blog | https://aws.amazon.com/blogs/architecture/overview-of-data-transfer-costs-for-common-architectures/ |

## Tier 2: depth when the decision needs it

| Title | Author | Year | Type | URL |
|---|---|---|---|---|
| Google Cloud Well-Architected Framework | Google | live | Official framework | https://cloud.google.com/architecture/framework |
| AWS Well-Architected lenses | AWS | live | Official guide | https://docs.aws.amazon.com/wellarchitected/latest/userguide/lenses.html |
| Implementing WAF recommendations (review cadence) | Microsoft | live | Guide | https://learn.microsoft.com/en-us/azure/well-architected/design-guides/implementing-recommendations |
| AWS compute / container decision guides | AWS | live | Official guides | https://docs.aws.amazon.com/decision-guides/latest/decision-guides/choosing-aws-compute-service.html |
| Where should I run my stuff? | Google Cloud | c. 2021 | Blog | https://cloud.google.com/blog/topics/developers-practitioners/where-should-i-run-my-stuff-choosing-google-cloud-compute-option |
| FinOps Unit Economics capability | FinOps Foundation | live | Framework | https://www.finops.org/framework/capabilities/unit-economics |
| Cloud FinOps, 2nd ed. | Storment & Fuller | 2023 | Book | https://www.finops.org/community/finops-book/ |
| Cloud Strategy | Gregor Hohpe | 2020+ | Book | https://architectelevator.com/book/ |
| Service Levels and Error Budgets | Jones & Murphy | 2016 | SREcon talk | https://www.usenix.org/conference/srecon16/program/presentation/jones |
| Building Secure and Reliable Systems (free) | Google | 2020 | Book | https://google.github.io/building-secure-and-reliable-systems/ |
| Cell-based architecture | AWS | 2023 | Whitepaper | https://docs.aws.amazon.com/wellarchitected/latest/reducing-scope-of-impact-with-cell-based-architecture/what-is-a-cell-based-architecture.html |
| CISA Zero Trust Maturity Model v2 | CISA | 2023 | Framework | https://www.cisa.gov/resources-tools/resources/zero-trust-maturity-model |
| BeyondCorp | Ward & Beyer | 2014 | Paper | https://research.google/pubs/beyondcorp-a-new-approach-to-enterprise-security/ |
| Threat Modeling Manifesto | various | 2020 | Manifesto | https://threatmodelingmanifesto.org |
| OWASP SAMM | OWASP | live | Maturity model | https://owaspsamm.org/ |
| NIST SSDF SP 800-218 | NIST | 2022 | Standard | https://csrc.nist.gov/pubs/sp/800/218/final |
| AWS IAM best practices | AWS | live | Official guide | https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html |
| Organizing your AWS environment using multiple accounts | AWS | live | Whitepaper | https://docs.aws.amazon.com/whitepapers/latest/organizing-your-aws-environment/organizing-your-aws-environment.html |
| Azure landing zone | Microsoft | live | Official guide | https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/ |
| Google Cloud resource hierarchy | Google | live | Official guide | https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy |
| NIST SP 800-61r3 incident response | NIST | 2025 | Standard | https://csrc.nist.gov/pubs/sp/800/61/r3/final |
| ASVS 5.0 for users of 4.0 (level definitions rethought) | OWASP | 2025 | Standard | https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x05-For-Users-Of-4.0.md |
| Platform engineering maturity model | CNCF | 2023 | Model | https://www.cncf.io/blog/2023/11/20/announcing-the-platform-engineering-maturity-model |
| OpenGitOps principles | CNCF OpenGitOps | 2021 | Spec | https://opengitops.dev/ |
| DORA metrics | DORA | live | Research guide | https://dora.dev/guides/dora-metrics/ |
| AWS Sustainability pillar | AWS | live | Framework | https://docs.aws.amazon.com/wellarchitected/latest/framework/sustainability.html |
| Infrastructure as Code, 3rd ed. | Kief Morris | — | Book (seen in search results only) | https://www.thoughtworks.com/en-us/insights/books/infrastructure-as-code-3rd-ed |

## Tier 3: case studies, critiques, counterpoints

| Title | Author | Year | Type | URL |
|---|---|---|---|---|
| Multi-Cloud is the Worst Practice | Corey Quinn | 2020 | Blog | https://www.lastweekinaws.com/blog/multi-cloud-is-the-worst-practice/ |
| Cloud Repatriation is Getting Complicated | Corey Quinn | 2025 | Blog | https://www.lastweekinaws.com/blog/cloud-repatriation-is-getting-complicated/ |
| Why we're leaving the cloud | DHH | 2022 | Blog | https://world.hey.com/dhh/why-we-re-leaving-the-cloud-654b47e0 |
| We have left the cloud | DHH | 2023 | Blog | https://world.hey.com/dhh/we-have-left-the-cloud-251760fb |
| 37signals cloud-exit savings, with analyst critique | Ars Technica | 2024 | News | https://arstechnica.com/information-technology/2024/10/basecamp-maker-37signals-says-its-cloud-exit-will-save-it-10m-over-5-years |
| Moving Mountains of Data off S3 | 37signals | 2025 | Podcast transcript | https://dev.37signals.com/moving-mountains-of-data-off-s3 |
| Prime Video moves from serverless to containers | InfoQ | 2023 | News | https://www.infoq.com/news/2023/05/prime-ec2-ecs-saves-costs |
| You (probably) don't need Kubernetes | Martin Tournoij | 2019 | Blog | https://www.arp242.net/dont-need-k8s.html |
| Kubernetes failure stories | community | live | Catalogue | https://k8s.af/ |
| Solo developer infrastructure playbook | Imad Rashid | c. 2026 | Blog | https://imad.website/writing/solo-developer-playbook/ |
| The Frugal Architect keynote (re:Invent 2023) | Werner Vogels | 2023 | Talk | https://www.youtube.com/watch?v=UTRBVPvzt9w |
