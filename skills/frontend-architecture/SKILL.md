---
name: frontend-architecture
description: Use when deciding web or client architecture. Rendering, state, design system, delivery, reach, quality gates.
metadata:
  version: "0.1.0"
  status: draft
  owner: maintainer
---

# Front-end architecture

How to make, and review, the structural decisions for a web or client application. This skill
adds the front-end forces. Use it alongside `architecture-decision-making` (method, decision
records, fitness functions), `architecture-research-and-verification` (sources, live checks,
confidence), `engineering-principles` (code level) and `web-accessibility-check` (UI acceptance).

## When to use

- A new web, PWA or cross-platform client, or a major re-platform or rewrite.
- Choosing or challenging a meta-framework, rendering mode, state approach, design-system scope,
  micro-frontend split, monorepo or mobile stack.
- Reviewing a front-end proposal, or explaining why an app is slow, fragile or costly to change.

Skip it for a component-level change inside a settled architecture.

## The decision sequence

Work through the steps in order; each limits the next, and skipping ahead (a framework before a
rendering style) is the most common failure. Record each significant decision with its rejected
options. State confidence with the shared low / medium / high rubric in
`architecture-research-and-verification`.

1. **Users, devices, network, content-vs-app.** Find out who loads this, on what hardware and
   connection, and how often. Place each route between content (read, share, rank in search) and
   app (long sessions, dense interaction, behind login). Without audience data, assume
   the latest global P75 baseline device and network (look it up at task time), not the
   developer's laptop. At P75 a quarter of users still have slower hardware: a floor, not a
   worst case. Before launch, budget with a lab proxy (throttled target device, transfer and JS
   limits); once traffic suffices, validate or replace it with segmented field P75 data. Keep lab
   and field gates distinct
   (see [references/budgets-and-quality-gates.md](references/budgets-and-quality-gates.md)).
2. **Rendering and activation, per route.** Decide four separate axes that can be combined:
   route rendering mode (static, revalidated, server, streamed, client); the server/client
   component boundary where the framework has one; hydration or activation strategy (full,
   islands, incremental, resumable, none); and state topology (do interactive regions share
   state? see step 3). On each axis pick the least client JavaScript that meets the need; mixing
   per route is normal. Default toward static or server rendering; full rehydration makes the
   user pay twice. Before choosing an immature or framework-specific mechanism, measure a
   prototype on the target device against the step 1 budget.
3. **Data and state.** Classify state before choosing tools. Server state is remote, shared and
   can go stale, so it belongs in a cache with revalidation or in framework loaders. URL state is
   for compact, stable, non-sensitive view state worth sharing or bookmarking (filters,
   pagination, selection); keep secrets, tokens, personal data and large payloads out, because
   URLs leak through logs, referrers and history. UI state stays colocated with its component.
   Global client state is whatever is truly cross-cutting, which is usually small. Pick the API
   shape from the client's point of view: REST, GraphQL, typed RPC, or a backend-for-frontend.
   Choose local-first sync only when offline work, multi-device use or real-time collaboration is
   a core requirement.
4. **Components and design system.** Layer it as tokens → core components → optional recipe
   components → product. Grow a layer only when a real consumer needs it. Keep tokens in a
   standard interchange format. Separate view code from domain code. Prefer native elements and
   plain functions or hooks; extract a headless component only when several visual renderers need
   the same interactive state machine and accessibility behaviour.
5. **Security and privacy boundaries.** Settle these before delivery; they constrain rendering,
   hosting, state and third parties. Classify browser-held data and mark trust boundaries. Choose
   session or token transport and its CSRF defence. Name the server layer that owns each
   authorization decision; the UI only hides what the server already denies. Govern third-party
   scripts. Decide privacy, consent and analytics scope, what may sit in browser storage, upload
   and download handling, and abuse controls. Map the result to current OWASP ASVS requirements
   at a risk-chosen level, and make each control an executable gate
   (see [references/security-and-privacy.md](references/security-and-privacy.md)).
6. **Delivery, build and repo.** Decide the module boundaries and enforce them with tooling.
   Choose monorepo or polyrepo based on shared code and release cadence. Decide how deploys,
   caching and preview environments work. Micro-frontends are earned mainly by autonomous teams
   owning independently deployable business boundaries; a legacy strangle (with a convergence
   and removal plan) or regulatory isolation can also qualify.
7. **Platform reach.** Choose among web, PWA, web-in-native-shell, a cross-platform native
   toolkit, or fully native. Weigh UX fidelity against reach and cost, and ask whether the
   toolkit's ceiling is high enough for the hardest feature on the roadmap. Choose an offline
   caching strategy for each resource class. Real offline *writes* push you back to step 3
   (local-first); a native client may change step 5's token transport.
8. **Cross-cutting constraints.** Accessibility, performance, observability and i18n change
   earlier choices, so make each an executable gate (budgets reference): WCAG 2.2 AA as the
   default floor, real user monitoring that covers client-side route changes, and `Intl` with
   logical CSS from day one.

Option tables for each step, with the forces behind them and when each option is wrong, are in
[references/decision-guides.md](references/decision-guides.md).

## Simplicity defaults and what earns complexity

Each default protects **simplicity and user outcomes**. Depart when the trigger is present, and
record it.

| Default | Value protected | Signs it is being violated | What earns the more complex option |
|---|---|---|---|
| Server/static HTML, little JS | Load speed on low-end devices; less code to maintain | A JS bundle growing every release; good FCP but poor INP | Long-session, highly interactive app behind login, with budget discipline |
| Server-state cache plus colocated UI state | One source of truth; no hand-written invalidation | API responses copied into a global store | Complex client-side domain logic, undo/audit, or large-team conventions |
| One deployable front end with enforced module boundaries | Payload size, consistency, low operational cost | Teams blocked on each other's releases | Autonomous teams owning independently deployable business subdomains; or a time-boxed legacy strangle with a removal plan |
| Tokens plus a component folder | Speed; avoids governance overhead | Design-system layers with one consumer | Several products or teams consuming the same components |
| Web/PWA first | Reach, linkability, one codebase | Native shell added with no feature that needs it | A hard requirement the web can't meet, confirmed against current platform support |
| Single BFF only when aggregation is heavy | Fewer moving parts | A web-only app with a thin pass-through BFF | Distinct client types (web, mobile, partner) or heavy server-side aggregation |
| Repo layout that matches sharing needs | Build speed; clear ownership | Monorepo without graph-aware tooling; polyrepo with copy-pasted code | Shared code, atomic cross-project changes, and an owner for CI and cache |

When *not* to lean on the defaults: the product is the interactivity itself (editor, canvas,
collaboration), audience data shows high-end devices, or an existing stack makes the default a
migration rather than a choice. Then weigh the switching cost explicitly.

## Anti-patterns

Format: principle broken, then how to spot it.

- **Framework-first selection.** Style before tool. Spot: a framework named before the audience
  or rendering mode.
- **SPA or full hydration by default.** Least JS that meets the need. Spot: a content route ships
  the whole app.
- **Budgets on developer hardware; lab numbers reported as field.** Measure real users. Spot:
  no throttled lab gate before launch, no segmented P75 field target after it.
- **Server data mirrored into a global store.** One source of truth. Spot: hand-written
  invalidation, stale data.
- **One rendering axis decides all the others.** Rendering mode, component boundary, activation
  and state topology are separate. Spot: islands chosen for an app-shaped product, then state
  passed between islands via events or cookies.
- **Micro-frontend anarchy.** Boundary justification. Spot: several frameworks or duplicate
  runtimes on one page.
- **Design-system or monorepo over-architecture.** Grow on real need. Spot: layers with no second
  consumer; a repo with no boundary rules.
- **Accessibility bolted on; ARIA instead of native elements.** Native semantics first. Spot:
  `div` buttons with role patches.
- **Allowlist CSP treated as done; unpinned dependencies.** Defence in depth. Spot: host lists
  with no nonce or hash; no lockfile or provenance policy.
- **Security by hidden buttons.** The server owns authorization. Spot: permissions checked only
  in UI code; tokens in script-readable storage; tag-manager scripts with no owner.
- **Assuming vitals tooling covers client-side route changes.** Observe what users experience.
  Spot: SPA monitoring that reports only the first load.
- **DX marketing as evidence.** User outcomes over sentiment. Spot: popularity cited, no field
  data.

## Check current state (do this at task time)

Front-end facts go stale within months. Verify these instead of recalling them, and record the
date checked: Baseline/caniuse status of every platform feature relied on; Core Web Vitals
definitions and thresholds; Web Almanac and CrUX (eligibility rules apply); the latest global
P75 budget estimate; framework docs, changelogs and RFCs for rendering and hydration semantics;
the current Technology Radar edition; WCAG and its legal status where the product ships; the
current OWASP ASVS and Top 10; privacy and consent law; mobile toolkit docs and iOS PWA support.
Survey data such as State of JS is a self-selected popularity signal, never evidence of quality.
Live URLs are in [references/reading-list.md](references/reading-list.md) under "Live sources".

## References

- [references/decision-guides.md](references/decision-guides.md): option tables for each step.
- [references/budgets-and-quality-gates.md](references/budgets-and-quality-gates.md): performance
  budgets, accessibility, security headers and testing shape as fitness functions.
- [references/security-and-privacy.md](references/security-and-privacy.md): step 5 principles,
  ASVS mapping method and security gates.
- [references/reading-list.md](references/reading-list.md): essential, deep-dive and reference
  sources with year and type, plus live sources.
