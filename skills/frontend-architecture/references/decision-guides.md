# Front-end decision guides

One table per decision in the SKILL.md sequence. Rows are options, not rankings. Named products
are *examples* of a category. Confirm current behaviour and maturity at task time (see "Check
current state" in SKILL.md). Every source was retrieved on 2026-11-19. Entries marked
**[dated]** were published before Nov 2024: the method is durable, but re-check any facts.

## 1. Rendering and activation (decide per route, per axis)

These are four separate axes, not one choice, and they combine: a server-rendered route can use
server components and incremental hydration; a static page can host islands; a client-rendered
app can still use a server/client component boundary for its data. Decide each axis on its own
forces.

Method: (1) classify each route by personalisation × interactivity × freshness; (2) set the lab
proxy budget, and the field budget once there is traffic (see budgets-and-quality-gates.md);
(3) on each axis pick the option with the least client JS that meets the need, mixing per route;
(4) before choosing an immature or framework-specific mechanism (server components, resumability,
incremental hydration), build a prototype on a throttled target device and measure LCP and Total
Blocking Time against the budget. Mixing modes per route is normal practice: https://web.dev/articles/rendering-on-the-web

### 1a. Route rendering mode (where and when HTML is produced)

| Option | Forces that pick it | When not | Sources |
|---|---|---|---|
| Static (SSG) | Same content for every user; changes at publish cadence; CDN-cacheable; consistent TTFB | Per-user personalisation; huge catalogues where rebuild time explodes | https://web.dev/articles/rendering-on-the-web |
| Incremental / on-demand revalidation | Mostly static pages that need freshness without a full rebuild | Strong per-request consistency; host lock-in, because the semantics are framework/host-specific | https://www.patterns.dev/react/incremental-static-rendering/ |
| SSR (multi-page) | Personalised or live data, SEO, low-end devices, modest interactivity | High compute cost at scale without HTML caching; heavy interactivity on top brings hydration cost | https://web.dev/articles/rendering-on-the-web |
| Streaming SSR | A fast shell plus slow data; earlier TTFB/FCP | Proxies or CDNs that buffer responses. Verify flush-through end to end | https://www.patterns.dev/react/streaming-ssr/ |
| CSR / SPA with app shell | Long-session, highly interactive tools behind login where SEO is irrelevant | Content sites; low-end audiences; teams without budget discipline. JS "tends to grow as an application grows" | https://web.dev/articles/rendering-on-the-web ; https://thoughtworks.com/radar/techniques/spa-by-default [dated] |
| Hypermedia-driven MPA | CRUD-heavy apps, small teams, server-centric skills | Offline use or rich client-side interaction | https://htmx.org/essays/a-response-to-rich-harris/ [dated] |

### 1b. Server/client component boundary (framework-dependent)

| Option | Forces that pick it | When not | Sources |
|---|---|---|---|
| Server components (one tree, server-only components ship no code) | Already on that framework; want server data access and zero-bundle components inside one tree | Not on that framework; team not ready for the client/server boundary model; hosting can't run a server | https://react.dev/reference/rsc/server-components ; https://www.patterns.dev/react/react-server-components/ ; https://overreacted.io/react-for-two-computers/ |
| No component boundary (classic SSR/CSR split) | Framework without server components; simple data needs | Server-only data access or large dependencies leaking into the client bundle | https://web.dev/articles/rendering-on-the-web |

### 1c. Hydration or activation strategy (how server HTML becomes interactive)

| Option | Forces that pick it | When not | Sources |
|---|---|---|---|
| None (no client JS) | Pure content; forms that work as plain HTML | Any client-side interactivity requirement | https://web.dev/articles/rendering-on-the-web |
| Full hydration | Small pages; framework default; interactivity everywhere | Content routes on low-end devices: the user pays for the app twice | https://web.dev/articles/rendering-on-the-web |
| Islands / partial hydration | Content-first pages with scattered widgets; zero JS for static regions; per-island hydration schedule | Interactive regions that share state, or one large interactive surface ("a collaborative editor, a Figma-like canvas") | https://jasonformat.com/islands-architecture/ [dated] ; https://docs.astro.build/en/concepts/islands/ ; https://www.patterns.dev/vanilla/islands-architecture/ |
| Incremental hydration + event replay | Already on a hydrating framework; want deferred hydration without losing early clicks | Not a substitute for shipping less code | https://angular.dev/guide/hydration |
| Resumability | Very large interactive pages where hydration cost dominates INP | Small ecosystem and hiring risk. Check maturity first | https://qwik.dev/docs/concepts/resumable/ |

### 1d. State topology

Ask whether interactive regions share state. Scattered, independent widgets suit islands; one
large shared-state surface suits an app-shaped activation strategy. The state classes themselves
are in §2.

Background on the forces:
- Hydration makes the user pay twice. Pages "can appear to be loaded and interactive, but can't actually respond to input". https://web.dev/articles/rendering-on-the-web
- Comparing islands, server components and resumability: https://playfulprogramming.com/posts/islands-server-components-resumability-oh-my-319d/ [dated]
- Cross-document View Transitions give MPAs SPA-like transitions (same-origin only): https://developer.chrome.com/docs/web-platform/view-transitions/cross-document
- "The best SPA is better than the best MPA. The average SPA is worse than the average MPA.": https://nolanlawson.com/2022/06/27/spas-theory-versus-practice/ [dated]
- The "transitional apps" middle path: https://geoffrich.net/posts/rich-harris-jamstack-conf-2021/ [dated]
- Open contradiction: no neutral benchmark corpus compares the "least JS" mechanisms. Decide each axis separately, and require a measured prototype on the target device before adopting an immature or framework-specific mechanism.

## 2. Data and state

| State class | Default home | Forces for something heavier | When not | Sources |
|---|---|---|---|---|
| Server state (remote, async, shared, can go stale) | A cache with stale-while-revalidate semantics, or framework loaders / server components | GraphQL normalised caches when many views share a graph | Copying it into a global store (hand-written invalidation) | https://tanstack.com/query/latest/docs/framework/react/guides/does-this-replace-client-state ; https://tkdodo.eu/blog/react-query-as-a-state-manager |
| URL state (compact, stable, non-sensitive view state: filters, pagination, selection) | The URL, so it is shareable, bookmarkable and survives reload | — | Ephemeral UI details; secrets, tokens, personal data, large payloads, or anything whose disclosure through server logs, referrers, analytics or browser history is unacceptable | Placement: *common practice, no primary source fetched*. Leakage via referrers: https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Referrer-Policy |
| UI state | Colocated with the component; lift only when needed | — | Global by default | https://kentcdodds.com/blog/application-state-management-with-react [dated] ; https://react.dev/learn/thinking-in-react |
| Global client state | A small store for truly cross-cutting data | Complex client domain logic, undo/audit, large-team conventions | Small apps ("You Might Not Need Redux") | https://redux.js.org/faq/general |
| Local-first / CRDT sync | A sync engine plus local store | Offline work, multi-device use, real-time collaboration, user ownership | Server must be authoritative per write (payments, inventory); huge unpartitionable data. The area is still maturing, so re-verify | https://www.inkandswitch.com/essay/local-first/ [dated] ; https://martin.kleppmann.com/2024/05/30/local-first-conference.html [dated] ; https://localfirstweb.dev/ |

## 3. API consumption (client view)

| Option | Forces for | When not | Sources |
|---|---|---|---|
| REST/HTTP + OpenAPI | Public or partner APIs; HTTP/CDN caching; polyglot clients | Many screen-specific aggregations make the client chatty (consider a BFF) | https://samnewman.io/patterns/architectural/bff/ [dated] |
| GraphQL | Many clients with differing data needs; graph-shaped domain; schema as contract | Small app with one client; reliance on HTTP caching; no capacity for schema governance. Its own docs say the practices "should not be taken as gospel" | https://graphql.org/learn/best-practices/ |
| Typed RPC (e.g. tRPC) | Full-stack TypeScript, same team or monorepo, "no code generation step" | Non-TS or public consumers; independently versioned services | https://trpc.io/docs |
| Backend-for-frontend | Distinct client experiences owned by the UI team; server-side aggregation | Web-only app with little aggregation. Newman: it makes sense only "if and when you have a significant amount of aggregation required on the server-side" | https://samnewman.io/patterns/architectural/bff/ [dated] |

## 4. Components and design system

| Option | Forces for | When not | Sources |
|---|---|---|---|
| Tokens + a component folder | One team, one product | Several products need shared, versioned components | https://bradfrost.com/blog/post/the-design-system-ecosystem/ [dated] |
| Layered ecosystem (tokens → core → recipe/smart → product) | Multiple products or teams consuming the same components | Layers added before real needs arise ("only as complex as it needs to be") | https://bradfrost.com/blog/post/the-design-system-ecosystem/ [dated] |
| Atomic design vocabulary | Shared language for composition | Used as a folder law. It "is not a linear process, but rather a mental model" | https://atomicdesign.bradfrost.com/chapter-2/ [dated] |
| Standard token format (W3C DTCG) | Tool interoperability; multi-platform outputs | No design tooling pipeline yet (still keep tokens named) | https://www.designtokens.org/tr/drafts/format/ ; https://www.w3.org/community/design-tokens/2025/10/28/design-tokens-specification-reaches-first-stable-version/ |
| View/domain separation | Testable domain logic; views that change independently of rules | Trivial components with no logic | https://martinfowler.com/articles/modularizing-react-apps.html [dated] |
| Headless components | Several visual renderers need the same interactive state machine and accessibility behaviour | A native element or a plain function/hook suffices (start there); one visual variant | https://martinfowler.com/articles/headless-component.html [dated] |

## 5. Delivery, build and repo

### Micro-frontends

| Option | Forces for | When not | Sources |
|---|---|---|---|
| Modular monolith with enforced boundaries (try first) | One or a few teams; you want the boundaries without the runtime cost | Teams that are genuinely blocked on each other's releases | https://nx.dev/docs/features/enforce-module-boundaries |
| Route-level split by team, or linked separate MPAs | Clear page ownership; a cheap form of independence | Shared interactive surfaces across teams | *Listed in the dossier as a cheaper alternative, with no specific source* |
| Build-time package composition | Shared release train is acceptable | You need independent deploys (this gives them up) | https://martinfowler.com/articles/micro-frontends.html [dated] |
| Server/edge composition | Independent teams with mostly server-rendered pages | Rich cross-fragment client state | https://martinfowler.com/articles/micro-frontends.html [dated] |
| Runtime composition (e.g. Module Federation) | Autonomous teams owning independently deployable subdomains (primary force); shared and deduplicated dependencies | A single team; a tight JS budget (duplicate runtimes); mixing frameworks on a page ("anarchy") | https://thoughtworks.com/en-us/radar/languages-and-frameworks/module-federation ; https://module-federation.io/guide/start/index.html ; https://www.thoughtworks.com/radar/techniques/micro-frontend-anarchy [dated] ; https://infoq.com/news/2025/04/microfrontend-best-practices |

Forces, in order of weight: (1) autonomous teams owning independently deployable business
boundaries is the primary, durable force; (2) strangling a legacy front end is a temporary force,
acceptable only with a written convergence and removal plan (target state, order of migration,
date or trigger for removing the shim); (3) regulatory or security isolation of one area can also
qualify. Jackson lists incremental upgrade of legacy apps as a benefit; Mezzalira frames the unit
as a business subdomain.

The costs, per Jackson: payload duplication ("download React n times"), environment differences,
and operational/governance complexity. Mezzalira defines a micro-frontend as "the technical
representation of a business subdomain". Sources as above.

### Repository layout

| Option | Forces for | When not | Sources |
|---|---|---|---|
| Monorepo + light task runner with content-hash caching | Shared code; the main need is fast pipelines ("never do the same work twice") | Nobody owns CI or remote-cache infrastructure | https://turborepo.com/docs/core-concepts/caching |
| Monorepo + graph-aware platform (generators, boundary rules) | Shared code, atomic cross-project changes, governance needs | Small repo where the tooling overhead exceeds the benefit | https://nx.dev/concepts/decisions/why-monorepos (vendor) ; https://monorepo.tools/ (vendor-sponsored) |
| Polyrepo with published packages | Independent teams, different release cadences, little shared code | Frequent cross-repo changes to the same contract | *Dossier judgment; no specific source.* Trade-off overview: https://monorepo.tools/ |

Without graph-aware tooling, a monorepo "gets slower and messier as it grows": https://nx.dev/concepts/decisions/why-monorepos

## 6. Platform reach

Method: weigh UX fidelity against reach and cost, and ask whether the toolkit's "ceiling is high
enough" for the hardest planned feature. https://martinfowler.com/articles/mobileImplStrategy.html [dated; method only]

| Option | What is shared / how UI renders | Pick when | When not | Sources |
|---|---|---|---|---|
| PWA | Everything; runs in the browser; "capable, reliable, installable" | Reach, linkable content, no store dependency, fast iteration | Deep OS integration; store presence is a hard requirement; iOS feature gaps (check current support) | https://web.dev/articles/what-are-pwas |
| Web in a native shell | The whole web UI, plus native plugins | An existing web app and team; internal tools where shipping matters more than feel | High-fidelity native feel; heavy animation | https://capacitorjs.com/docs/getting-started/faqs |
| Cross-platform, platform-native views | JS/TS logic; renders native views | Team already in that ecosystem; native look | Pixel-identical custom UI; heavy GPU work | https://reactnative.dev/architecture/landing-page ; https://expo.dev/articles/choosing-a-mobile-stack/cross-platform-mobile-development (vendor) |
| Cross-platform, self-drawn UI | One language; draws its own UI | Brand-custom UI consistent across platforms | Platform-native widgets by default; web SEO | https://docs.flutter.dev/resources/architectural-overview |
| Shared logic, native UI | Shares non-UI code | Teams sharing domain logic across native apps | Want one UI codebase quickly | https://expo.dev/articles/choosing-a-mobile-stack/cross-platform-mobile-development (vendor) |
| Fully native | Nothing | Best fidelity; platform-first features; performance ceiling | Small team under two-platform budget pressure | https://martinfowler.com/articles/mobileImplStrategy.html [dated] |

### Offline / service worker caching (per resource class)

| Resource class | Strategy | Sources |
|---|---|---|
| Versioned static assets | Cache-first | https://developer.chrome.com/docs/workbox/caching-strategies-overview |
| HTML and fresh data | Network-first | https://web.dev/articles/offline-cookbook |
| Data that tolerates staleness | Stale-while-revalidate | https://developer.chrome.com/docs/workbox/caching-strategies-overview |
| Writes, auth | Network-only (offline writes mean local-first, see §2) | https://web.dev/articles/offline-cookbook |

Design the update UX. Service worker lifecycle and update semantics are a common failure source:
https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers

## 7. Cross-cutting: i18n

- Use the platform's `Intl` for dates, numbers, plurals, collation, lists and relative time: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Internationalization ; https://www.w3.org/International/articles/intl/index
- Externalised messages, locale in routing, logical CSS properties for RTL, and no string concatenation. *Standard practice. Not verified against a primary source in the dossier.* W3C i18n specs index: https://www.w3.org/TR/international-specs/ (search-verified only).

Security, accessibility, performance, testing and observability gates are in
[budgets-and-quality-gates.md](budgets-and-quality-gates.md).
