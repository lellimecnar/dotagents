# Front-end architecture reading list

All URLs were retrieved 2026-11-19. Tiers: **essential** (read before a major front-end
decision), **deep-dive** (when that decision is on the table), **reference** (look up as needed).
**[dated]** marks material published before Nov 2024: durable method, re-check facts. Vendor
sources are marked, so weigh their bias.

## Essential

| Title | Author | Year | Type | URL |
|---|---|---|---|---|
| Rendering on the Web | Addy Osmani, Jason Miller | 2019, updated 2026 | Article | https://web.dev/articles/rendering-on-the-web |
| Rendering patterns | Lydia Hallie, Addy Osmani | ongoing | Book/site | https://www.patterns.dev/vanilla/rendering-patterns/ |
| SPA by default (Hold) | Thoughtworks Radar | 2022 [dated] | Radar | https://thoughtworks.com/radar/techniques/spa-by-default |
| SPAs: theory versus practice | Nolan Lawson | 2022 [dated] | Blog | https://nolanlawson.com/2022/06/27/spas-theory-versus-practice/ |
| Islands, Server Components, Resumability, Oh My! | Ryan Carniato | 2023 [dated] | Essay | https://playfulprogramming.com/posts/islands-server-components-resumability-oh-my-319d/ |
| Web Vitals | web.dev | ongoing | Docs | https://web.dev/articles/vitals |
| The Performance Inequality Gap, 2024 | Alex Russell | 2024 [dated] | Blog | https://infrequently.org/2024/01/performance-inequality-gap-2024/ |
| The Market for Lemons | Alex Russell | 2023 [dated] | Blog | https://infrequently.org/2023/02/the-market-for-lemons/ |
| Does TanStack Query replace client state? | TanStack | ongoing | Docs | https://tanstack.com/query/latest/docs/framework/react/guides/does-this-replace-client-state |
| The Design System Ecosystem | Brad Frost | 2023 [dated] | Blog | https://bradfrost.com/blog/post/the-design-system-ecosystem/ |
| Micro Frontends | Cam Jackson | 2019 [dated] | Article | https://martinfowler.com/articles/micro-frontends.html |
| Backends For Frontends | Sam Newman | 2015 [dated] | Pattern | https://samnewman.io/patterns/architectural/bff/ |
| WCAG 2.2 | W3C | 2023 [dated] | Standard | https://www.w3.org/TR/WCAG22/ |
| Strict CSP | web.dev | ongoing | Docs | https://web.dev/articles/strict-csp |
| Soft navigations | Chrome team | ongoing | Docs | https://developer.chrome.com/docs/web-platform/soft-navigations |

## Deep-dive

| Title | Author | Year | Type | URL | Read when |
|---|---|---|---|---|---|
| Islands Architecture | Jason Miller | 2020 [dated] | Blog | https://jasonformat.com/islands-architecture/ | Considering islands |
| Islands architecture | patterns.dev | ongoing | Article | https://www.patterns.dev/vanilla/islands-architecture/ | Islands limits (shared state) |
| React Server Components | patterns.dev | ongoing | Article | https://www.patterns.dev/react/react-server-components/ | RSC vs SSR |
| React for Two Computers | Dan Abramov | 2025 | Essay | https://overreacted.io/react-for-two-computers/ | The RSC mental model |
| Resumable | Qwik team | ongoing | Docs | https://qwik.dev/docs/concepts/resumable/ | Hydration cost dominates |
| Hydration is Pure Overhead | Miško Hevery | 2022 [dated] | Blog (vendor) | https://www.builder.io/blog/hydration-is-pure-overhead | Critique of hydration |
| Transitional apps (talk transcript) | Rich Harris, transcribed by Geoff Rich | 2021 [dated] | Talk | https://geoffrich.net/posts/rich-harris-jamstack-conf-2021/ | SPA/MPA middle path |
| A response to "Have SPAs ruined the web?" | htmx | 2021 [dated] | Essay | https://htmx.org/essays/a-response-to-rich-harris/ | Hypermedia counterpoint |
| The cost of JavaScript in 2019 | Addy Osmani | 2019 [dated] | Blog | https://v8.dev/blog/cost-of-javascript-2019 | JS cost model |
| Performance budgets 101 | web.dev | 2018+ [dated] | Docs | https://web.dev/articles/performance-budgets-101 | Setting budgets |
| Local-first software | Kleppmann, Wiggins, van Hardenberg, McGranaghan | 2019 [dated] | Peer-reviewed (Onward!) | https://www.inkandswitch.com/essay/local-first/ | Offline or collaboration is core |
| Local-first: past, present, future | Martin Kleppmann | 2024 [dated] | Talk | https://martin.kleppmann.com/2024/05/30/local-first-conference.html | Local-first maturity |
| React Query as a State Manager | Dominik Dorfmeister | 2021 [dated] | Blog | https://tkdodo.eu/blog/react-query-as-a-state-manager | Server-state caching |
| Application State Management with React | Kent C. Dodds | 2020 [dated] | Blog | https://kentcdodds.com/blog/application-state-management-with-react | Colocation |
| Atomic Design, ch. 2 | Brad Frost | 2016 [dated] | Book | https://atomicdesign.bradfrost.com/chapter-2/ | Component vocabulary |
| Design Tokens Format Module | W3C DTCG | 2025 | Spec (CG) | https://www.designtokens.org/tr/drafts/format/ | Token pipeline |
| Modularizing React Applications | Juntao Qiu | 2023 [dated] | Article | https://martinfowler.com/articles/modularizing-react-apps.html | View/domain layering |
| Headless Component | Juntao Qiu | 2023 [dated] | Article | https://martinfowler.com/articles/headless-component.html | Behaviour reuse |
| Micro frontend anarchy | Thoughtworks Radar | 2020 [dated] | Radar | https://www.thoughtworks.com/radar/techniques/micro-frontend-anarchy | MFE failure mode |
| Micro-frontend best practices (Mezzalira) | InfoQ | 2025 | Talk report | https://infoq.com/news/2025/04/microfrontend-best-practices | Subdomain-aligned MFEs |
| Why monorepos | Nx | ongoing | Docs (vendor) | https://nx.dev/concepts/decisions/why-monorepos | Monorepo trade-offs |
| Selecting a Mobile Implementation Strategy | Giles Alexander | 2012 [dated] | Article | https://martinfowler.com/articles/mobileImplStrategy.html | Mobile decision method |
| Cross-platform mobile development | Expo | ~2025 | Article (vendor) | https://expo.dev/articles/choosing-a-mobile-stack/cross-platform-mobile-development | Options matrix |
| Offline cookbook | Jake Archibald / web.dev | updated | Docs | https://web.dev/articles/offline-cookbook | Offline strategies |
| XSS Prevention Cheat Sheet | OWASP | ongoing | Guide | https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html | Output encoding |
| Trusted Types | web.dev | ongoing | Docs | https://web.dev/articles/trusted-types | DOM-XSS sinks |
| Session Management Cheat Sheet | OWASP | ongoing | Guide | https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html | Session transport |
| CSRF Prevention Cheat Sheet | OWASP | ongoing | Guide | https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html | Cookie-authenticated writes |
| OAuth 2.0 for Browser-Based Applications (RFC 10017) | IETF | 2026 | Standard | https://www.rfc-editor.org/rfc/rfc10017.html | Tokens in a browser app; BFF pattern |
| Third Party JavaScript Management Cheat Sheet | OWASP | ongoing | Guide | https://cheatsheetseries.owasp.org/cheatsheets/Third_Party_Javascript_Management_Cheat_Sheet.html | Tags, widgets, analytics |
| Authorization Cheat Sheet | OWASP | ongoing | Guide | https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html | Authorization ownership |
| HTML5 Security Cheat Sheet | OWASP | ongoing | Guide | https://cheatsheetseries.owasp.org/cheatsheets/HTML5_Security_Cheat_Sheet.html | Browser storage |
| File Upload Cheat Sheet | OWASP | ongoing | Guide | https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html | User files |
| User Privacy Protection Cheat Sheet | OWASP | ongoing | Guide | https://cheatsheetseries.owasp.org/cheatsheets/User_Privacy_Protection_Cheat_Sheet.html | Privacy and analytics |
| Practical Test Pyramid | Ham Vocke | 2018 [dated] | Article | https://martinfowler.com/articles/practical-test-pyramid.html | Test shape |
| The Testing Trophy | Kent C. Dodds | 2021 [dated] | Blog | https://kentcdodds.com/blog/the-testing-trophy-and-testing-classifications | Test shape |

## Reference

| Title | Author | Year | Type | URL |
|---|---|---|---|---|
| Server Components reference | React team | ongoing | Docs | https://react.dev/reference/rsc/server-components |
| Islands (Astro docs) | Astro | ongoing | Docs | https://docs.astro.build/en/concepts/islands/ |
| Hydration guide | Angular team | ongoing | Docs | https://angular.dev/guide/hydration |
| Understanding React Server Components | Vercel | 2023 [dated] | Blog (vendor) | https://vercel.com/blog/understanding-react-server-components |
| Cross-document view transitions | Chrome team | ongoing | Docs | https://developer.chrome.com/docs/web-platform/view-transitions/cross-document |
| INP / LCP / CLS | web.dev | ongoing | Docs | https://web.dev/articles/inp ; https://web.dev/articles/lcp ; https://web.dev/articles/cls |
| Optimize long tasks | web.dev | ongoing | Docs | https://web.dev/articles/optimize-long-tasks |
| What's new in WCAG 2.2 | W3C WAI | 2023 [dated] | Standard companion | https://www.w3.org/WAI/standards-guidelines/wcag/new-in-22/ |
| ARIA Authoring Practices Guide | W3C WAI | ongoing | Guide | https://www.w3.org/WAI/ARIA/apg/ |
| Redux FAQ: General | Redux maintainers | ongoing | Docs | https://redux.js.org/faq/general |
| GraphQL best practices | GraphQL Foundation | ongoing | Docs | https://graphql.org/learn/best-practices/ |
| tRPC docs | tRPC | ongoing | Docs | https://trpc.io/docs |
| Enforce module boundaries | Nx | ongoing | Docs (vendor) | https://nx.dev/docs/features/enforce-module-boundaries |
| Caching | Turborepo | ongoing | Docs (vendor) | https://turborepo.com/docs/core-concepts/caching |
| monorepo.tools | Nx-sponsored | ongoing | Comparison (vendor) | https://monorepo.tools/ |
| Module Federation (Radar) | Thoughtworks | ~2024 | Radar | https://thoughtworks.com/en-us/radar/languages-and-frameworks/module-federation |
| What are PWAs? | web.dev | ongoing | Docs | https://web.dev/articles/what-are-pwas |
| Workbox caching strategies | Chrome team | ongoing | Docs | https://developer.chrome.com/docs/workbox/caching-strategies-overview |
| Flutter architectural overview | Google | ongoing | Docs | https://docs.flutter.dev/resources/architectural-overview |
| React Native architecture | Meta | ongoing | Docs | https://reactnative.dev/architecture/landing-page |
| CSP Cheat Sheet | OWASP | ongoing | Guide | https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html |
| OWASP Top 10:2025 | OWASP | 2025 | Standard | https://owasp.org/Top10/2025/0x00_2025-Introduction |
| ASVS 5.0.0: What is the ASVS | OWASP | 2025 | Standard | https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x03-What-is-the-ASVS.md |
| Abuse Case Cheat Sheet | OWASP | ongoing | Guide | https://cheatsheetseries.owasp.org/cheatsheets/Abuse_Case_Cheat_Sheet.html |
| Subresource Integrity | MDN | ongoing | Docs | https://developer.mozilla.org/en-US/docs/Web/Security/Defenses/Subresource_Integrity |
| Referrer-Policy | MDN | ongoing | Docs | https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Referrer-Policy |
| web-vitals library | GoogleChrome | ongoing | Code | https://github.com/googlechrome/web-vitals |
| OpenTelemetry browser | OpenTelemetry | ongoing | Docs | https://opentelemetry.io/docs/languages/js/getting-started/browser/ |
| Intl guide | MDN | ongoing | Docs | https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Internationalization |
| 7 Principles of Rich Web Applications | Guillermo Rauch | 2014 [dated] | Essay | https://rauchg.com/2014/7-principles-of-rich-web-applications |

## Live sources (check at task time and record the date)

| Question | Source | What to verify |
|---|---|---|
| Can I depend on browser feature X? | https://web.dev/baseline ; https://web-platform-dx.github.io/web-features/ ; caniuse | Newly or widely available; whether a polyfill is needed |
| What do real sites ship, and how fast are they? | https://almanac.httparchive.org/en/2024/javascript (look for the newest edition) ; https://developer.chrome.com/docs/crux/methodology | Current medians; whether your origin is CrUX-eligible |
| Metric definitions and thresholds | https://web.dev/articles/vitals | Any metric replaced; thresholds unchanged |
| Current global performance budget | https://infrequently.org/ | Newest "Performance Inequality Gap" edition |
| Framework usage and satisfaction | https://stateofjs.com/en-US | Sentiment only. The sample is self-selected, not a measure of quality |
| Industry adopt/hold signals | Thoughtworks Technology Radar | That the item is on the *current* edition |
| Framework rendering semantics | react.dev ; https://angular.dev/guide/hydration ; https://docs.astro.build/en/concepts/islands/ ; https://qwik.dev/docs/concepts/resumable/ ; changelogs and RFCs | Actual current behaviour of each shortlisted option |
| Accessibility standard and legal status | https://www.w3.org/TR/WCAG22/ ; https://digital-strategy.ec.europa.eu/en/policies/latest-changes-accessibility-standard | Current WCAG version and what the law references |
| Design tokens spec status | https://www.w3.org/community/design-tokens/ | Stable format version |
| Security taxonomy | https://owasp.org/Top10/2025/0x00_2025-Introduction ; OWASP cheat sheets | Current edition |
| Security verification requirements | https://owasp.org/www-project-application-security-verification-standard/ ; https://github.com/OWASP/ASVS | Current ASVS version; web-frontend, session, authorization, token and file-handling chapters |
| Browser-app token guidance | https://www.rfc-editor.org/rfc/rfc10017.html | Whether a newer revision or BCP supersedes it |
| Local-first ecosystem | https://localfirstweb.dev/ | Sync-engine maturity |
| Mobile toolkit internals | https://reactnative.dev/architecture/landing-page ; https://docs.flutter.dev/resources/architectural-overview ; https://capacitorjs.com/docs/getting-started/faqs | Current architecture and platform gaps |
