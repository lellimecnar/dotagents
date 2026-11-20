# dotagents

Shared, tool-neutral skills for AI agents: generalized methods any agent of a given type can use
(any code reviewer, fiction editor, schedule planner...). Project facts stay in each project's own skills;
those point back here for method.

Layout follows the Agent Skills spec and the Hermes tap format: one flat `skills/<name>/SKILL.md` per skill.

## Skills by agent type

Grouped in `skills.sh.json` (Hermes shows these as hub categories). Each profile installs the groups that match its job.

### Common (every agent) (5)

| Skill | Use when |
| --- | --- |
| `due-diligence-before-deciding` | choosing between two or more options (advising, presenting choices, or deciding mid-task). Do the due diligence first |
| `finish-the-whole-request` | always. Finish the whole request before you stop; escalate to other agents before asking the owner |
| `fixing-a-wrong-memory` | you are corrected or find a stored memory is wrong. Delete every copy, save once |
| `subagent-model-choice` | delegating a subtask. Pick the cheapest model that can do it well |
| `use-existing-solutions-first` | before building anything. Find the built-in or existing solution first |

### Engineering (15)

| Skill | Use when |
| --- | --- |
| `audio-pipeline-verification` | building or reviewing TTS/audio render or export. Measure, don't echo |
| `backlog-grooming` | grooming a backlog: one state per item, verify claims, owner unblocks |
| `coding-task-intake` | taking a coding task end to end: intake, build, verify, PR, report |
| `dependency-update-review` | triaging bot dependency PRs or lockfile changes. Reconcile, verdict |
| `docs-drift-sweep` | keeping project docs in step with merged code, or reviewing a docs PR |
| `engineering-principles` | writing, reviewing or fixing code. Principles plus review and fix-the-class protocols |
| `initiative-inventory-mapping` | mapping an in-flight initiative into proposed tracker tasks |
| `local-web-app-hardening` | building or reviewing a loopback/local web app. Boundaries, limits |
| `merge-queue-stewardship` | admitting approved PRs to a merge queue or checking trains flowed |
| `pull-request-review` | reviewing another agent's PR. Verify claims, pin head, one verdict |
| `qa-acceptance-evidence` | QA-accepting a change. Oracle, risk-ranked tests, evidence verdicts |
| `tech-debt-ranking` | producing a recurring ranked, deduped tech-debt backlog. No code edits |
| `test-lanes-and-coverage` | judging a test suite: lanes, branch-coverage ratchet, perf budgets |
| `web-accessibility-check` | building, reviewing or accepting UI. WCAG 2.2 AA checks and tools |
| `work-planning` | a plan or spec is requested, or uncertainty must be resolved before work |

### Architecture (6)

| Skill | Use when |
| --- | --- |
| `architecture-decision-making` | choosing, changing or reviewing an architecture or technology. Frame, compare, decide, record |
| `architecture-research-and-verification` | an architecture choice rests on facts or reasoning. Research, grade, verify, self-interrogate |
| `architecture-proposal-writing` | presenting an architecture recommendation to a decision owner. Answer-first brief plus ADR |
| `frontend-architecture` | deciding web or client architecture. Rendering, state, design system, delivery, reach, quality gates |
| `backend-and-data-architecture` | shaping services, APIs, data stores or integration. Domain-first sequence, boring defaults |
| `cloud-and-infrastructure-architecture` | choosing hosting, infra, DR, security or cloud cost. Fit options to forces; price lock-in |

### Ops (2)

| Skill | Use when |
| --- | --- |
| `alert-triage` | an alert webhook wakes you. Dedupe by fingerprint, diagnose read-only |
| `readonly-infrastructure-inspection` | inspecting servers, storage, backups or networks with read-only keys |

### Writing & book production (17)

| Skill | Use when |
| --- | --- |
| `chapter-sequence-renumbering` | chapters are added, removed, merged or split. Safe renames, refs |
| `conlang-usage-review` | reviewing invented-language use in fiction. Form, meaning, gating |
| `dialogue-voice-review` | reviewing dialogue, character voice or relationship beats in fiction |
| `editorial-production-management` | planning a book's editing/publishing pipeline. Stages, gates, routes |
| `fiction-manuscript-editing` | editing fiction: developmental, line, copy. Staged passes, notes |
| `fiction-pacing-review` | reviewing fiction pacing, structure, tension or hooks. Diagnostics |
| `fiction-prose-craft` | drafting or revising fiction scenes. Outside-in craft tests, fixes |
| `layered-chapter-drafting` | drafting a chapter from an outline. Brief, layered passes, gates |
| `manuscript-book-build` | building or verifying DOCX/EPUB from chapters. Inspect real output |
| `manuscript-readiness-assessment` | judging if a chapter is publish-ready. Full rubric, ranked fixes |
| `multi-model-drafting` | drafting prose with several models. Fan out, merge blind, notes |
| `markdown-section-extraction` | pulling sections from big markdown files by heading. Script |
| `originality-check` | checking a manuscript for copied text or derivative concepts |
| `prose-text-analysis` | measuring prose: readability, n-grams, overuse, avoid-list hits |
| `science-plausibility-review` | reviewing science/tech plausibility in fiction. Smallest fix |
| `story-continuity-audit` | checking story canon/continuity against prose. Cited ledgers |
| `story-expertise-seeding` | a book-team agent must build cited story and field expertise |

### Organizing (6)

| Skill | Use when |
| --- | --- |
| `clutter-feelings-and-hoarding` | clutter work hits sentiment, guilt, family conflict or hoarding signs |
| `decluttering-method-guide` | choosing a decluttering method, room order, session plan or keep rule |
| `household-paper-flow` | setting up a home paper flow, record retention, scanning or shredding |
| `organizing-for-adhd-households` | designing organizing systems for ADHD, sensory or support-needs homes |
| `room-organizing-playbooks` | organizing a specific room or zone, or when storage safety is in play |
| `unwanted-item-routing` | deciding if an unwanted item is sold, donated, recycled or trashed |

### Planning (2)

| Skill | Use when |
| --- | --- |
| `capacity-first-scheduling` | building or repairing a week or day of time boxes, ADHD-aware |
| `task-triage-and-rating` | capturing, rating, ranking or breaking down tasks, incl. from email |

### Finance (2)

| Skill | Use when |
| --- | --- |
| `household-money-guidance` | answering household money, bill, debt or cash-flow questions |
| `reconciled-spending-reports` | reporting household spending, income or balances to the exact cent |

### Personal assistant (6)

| Skill | Use when |
| --- | --- |
| `daily-brief` | writing a person's morning brief or building today's plan for them |
| `evening-check-in` | running an evening check-in, a reflection or a goal review |
| `household-request-capture` | someone asks their assistant to track a chore, home item or project |
| `owned-calendar-stewardship` | an assistant owns a person's calendar and must plan, mirror or fix it |
| `person-profile-seeding` | building or refreshing a person's notes from their read-only accounts |
| `smart-home-request-handling` | relaying or doing a request to read or change smart-home devices |

### Lance-facing (1)

| Skill | Use when |
| --- | --- |
| `i-have-adhd` | 'Shape output for a reader with ADHD: lead with the next action, number multi-step work, restate state across  |

## Using it

- **Hermes:** `hermes skills tap add lellimecnar/dotagents`, then `hermes skills install lellimecnar/dotagents/skills/<name>`
  for each skill the profile needs. `hermes skills update` (run on a schedule) pulls merged changes; locally edited
  copies are skipped and reported by `hermes skills check`, which is how agent improvements are found for promotion.
- **Other harnesses:** clone or download and point the skills directory at `skills/`, or copy the skills you need.

## Contributing

- Generalize: the method or expertise any agent of that type needs. Project facts (people, repos, paths, accounts,
  story content, household details) go in the project's own skill, which starts with
  "Method: follow the shared `<skill>` skill".
- Principles with judgment, not rigid rules. Keep sources cited. Under ~12 KB per SKILL.md; depth in `references/`.
- This repo is public: nothing private, no credentials, no hostnames or internal addresses.
- Changes arrive by pull request (normal branch + PR); the repository owner merges. Add new skills to a group in `skills.sh.json`.
