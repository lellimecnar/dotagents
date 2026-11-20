# Budgets and quality gates (fitness functions)

These turn front-end quality goals into objective, automatable checks, which is the
fitness-function idea from `architecture-decision-making`. Each gate lists the value it protects,
what to measure, where to enforce it, and when not to apply it. Published thresholds appear with
their source. Confirm them at task time because metrics and thresholds change. Sources were
retrieved 2026-11-19. **[dated]** marks material published before Nov 2024.

Lab and field gates are different instruments. Before launch there is no field data, so gate on a
lab proxy: a throttled target-device run plus transfer and JS budgets. After launch, once traffic
is sufficient (and, for public comparison, CrUX-eligible), validate or replace the lab budget with
field P75 data segmented by device class. Never report a lab number as a field result.

## 1. Performance

| Gate | Value protected | Measure / threshold | Enforce where | When not / caveats | Sources |
|---|---|---|---|---|---|
| Core Web Vitals in the field (post-launch) | User-perceived speed and stability | "Good" is LCP ≤ 2.5 s, INP ≤ 200 ms, CLS ≤ 0.1, at the **75th percentile**, split by mobile and desktop. INP is "poor" above 500 ms | Real user monitoring dashboard; release review | Needs enough real traffic; before that, use the lab proxy row. SPA route changes are not fully covered (see §4). Re-check the definitions, because INP replaced FID | https://web.dev/articles/vitals ; https://web.dev/articles/inp ; https://web.dev/articles/lcp ; https://web.dev/articles/cls |
| Transfer and JS budget for the target device (lab, from day one) | Load time on the P75 baseline device | Gather audience data first (analytics, RUM, CrUX, or ask the owner); use the global baseline only when none exists. Russell's 2024 figures for a first load under 5 s at p75: ~2.5 MiB total with ~100 KiB JS (markup-centric stacks) vs ~1.3 MiB total with ~650 KiB JS (JS-heavy). At 3 s: 1.4 MiB / 75 KiB vs 730 KiB / 365 KiB. "Until and unless teams have better data about their audience, the global baseline budget should be enforced." | CI bundle-size check failing the build; per-route budgets | You have real audience data showing faster devices. Replace the figures with the latest yearly edition | https://infrequently.org/2024/01/performance-inequality-gap-2024/ [dated] ; https://web.dev/articles/performance-budgets-101 |
| Bundle-size diagnostic | Parse and execute cost | Initial diagnostic only: the 2019 source suggested splitting bundles above ~50–100 kB, because JS cost is mostly download plus main-thread execution. Set actual route budgets from a current target-device benchmark and the task-time global baseline | Bundle analyser in CI | Engine and network economics change; never use the 2019 figure as a pass/fail gate | https://v8.dev/blog/cost-of-javascript-2019 [dated] |
| Long tasks | Responsiveness (INP) | Tasks over 50 ms count as "long tasks". Yield to the main thread | Lab traces in CI; long-animation attribution in RUM | — | https://web.dev/articles/optimize-long-tasks |
| Pre-launch lab proxy | Prevents budgets being set on developer hardware | LCP plus Total Blocking Time (the lab proxy for INP) on a throttled target device; prototype before committing to a rendering or activation mechanism | CI lab run; spike exit criterion | Lab results do not prove field results; replace with field P75 when available | https://web.dev/articles/rendering-on-the-web ; https://web.dev/articles/vitals |

Context: the median JS per page was 558 KB (mobile) and 613 KB (desktop) in 2024, up 14%. Most
sites exceed the baseline, so a gate is needed to stop growth.
https://almanac.httparchive.org/en/2024/javascript

## 2. Accessibility

| Gate | Value protected | Measure | Enforce where | When not / caveats | Sources |
|---|---|---|---|---|---|
| WCAG 2.2 Level AA as the default floor | Equal access; legal compliance | The success criteria, including 2.4.11 Focus Not Obscured (AA) and 2.5.8 Target Size (Minimum) (AA), two of the 9 criteria new in 2.2 | Component tests by role and name, automated scan in CI, manual pass at acceptance (`web-accessibility-check`) | Legal requirements vary. In the EU the reference is EN 301 549, and a new WCAG version does not apply automatically until it is harmonised | https://www.w3.org/TR/WCAG22/ ; https://www.w3.org/WAI/standards-guidelines/wcag/new-in-22/ ; https://digital-strategy.ec.europa.eu/en/policies/latest-changes-accessibility-standard |
| Native semantics first; vetted ARIA patterns for custom widgets | Robust assistive-tech support | "No ARIA is better than Bad ARIA"; custom widgets follow the APG pattern | Lint plus review | — | https://www.w3.org/WAI/ARIA/apg/practices/read-me-first/ ; https://www.w3.org/WAI/ARIA/apg/patterns/ |

An automated scan cannot declare a pass. The manual checklist lives in `web-accessibility-check`.
For the state of the field: https://almanac.httparchive.org/en/2024/accessibility

## 3. Security (client side)

| Gate | Value protected | Measure | Enforce where | When not / caveats | Sources |
|---|---|---|---|---|---|
| Context-aware output encoding | XSS prevention (first layer) | Framework auto-escaping kept on; raw-HTML sinks reviewed | Lint rule on raw-HTML APIs; review | — | https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html |
| Strict CSP (nonce- or hash-based) | Defence in depth against XSS | A CSP header with nonces or hashes. Host-allowlist CSPs "can be bypassed in most configurations" | Response-header test in CI or at deploy; report-only first | Static hosting without per-response nonces means using hashes. Plan for it in the rendering choice | https://web.dev/articles/strict-csp ; https://cheatsheetseries.owasp.org/cheatsheets/Content_Security_Policy_Cheat_Sheet.html |
| Trusted Types | Locks down DOM-XSS sinks | `require-trusted-types-for` in the CSP; violations reported | Header test plus violation reports | Check Baseline status for target browsers | https://web.dev/articles/trusted-types ; https://developer.mozilla.org/en-US/docs/Web/API/Trusted_Types_API |
| Dependency supply-chain control | Integrity of a large dependency tree | Software Supply Chain Failures is A03 in OWASP Top 10:2025. Its prevention list: generate and manage an SBOM, track transitive dependencies, remove unused ones, prefer signed packages, ensure artifact integrity via provenance and signing, and keep a patch-management process for updates. Install from the lockfile only (`npm ci` fails if the lockfile and `package.json` disagree, instead of updating the lockfile) | Dependency policy in CI; frozen-lockfile install; update review (`dependency-update-review`) | Equivalent frozen-lockfile installs exist in other package managers; check their current docs | https://owasp.org/Top10/2025/A03_2025-Software_Supply_Chain_Failures/ ; https://docs.npmjs.com/cli/v11/commands/npm-ci (both checked 2026-11-19) |

Session and token transport, CSRF, authorization, third-party scripts, consent, browser storage,
uploads, abuse limits and ASVS traceability gates are in
[security-and-privacy.md](security-and-privacy.md) §3.

## 4. Observability

| Gate | Value protected | Measure | Sources |
|---|---|---|---|
| Field metrics with attribution | Real-user outcomes beyond lab data | Collect CWV with attribution from real sessions | https://github.com/googlechrome/web-vitals |
| SPA route-change coverage | Honest numbers for client-side navigation | SPAs have "never been fully supported by the Core Web Vitals metrics". Track the soft-navigation work and instrument route changes yourself | https://developer.chrome.com/docs/web-platform/soft-navigations |
| Public baseline | Comparison against the wider web | CrUX, subject to eligibility rules | https://developer.chrome.com/docs/crux/methodology |
| Vendor-neutral tracing | Front-end to back-end correlation | OpenTelemetry browser instrumentation. Its docs say client instrumentation for the browser "is experimental and mostly unspecified" (checked 2026-11-19); confirm the status at task time | https://opentelemetry.io/docs/languages/js/getting-started/browser/ |

## 5. Testing shape

Shape the suite by confidence per unit cost and by failure mode. The pyramid and the trophy agree
on the main point: keep slow, brittle end-to-end tests few. Their disagreement is partly about
what "unit" means in UI code.

- Static types and lint are cheap confidence ("ESLint can get you a remarkable amount of confidence"): https://kentcdodds.com/blog/write-tests ; https://kentcdodds.com/blog/static-vs-unit-vs-integration-vs-e2e-tests
- Trophy, "mostly integration": https://kentcdodds.com/blog/the-testing-trophy-and-testing-classifications
- Pyramid; avoid the "ice-cream cone": https://martinfowler.com/bliki/TestPyramid.html ; https://martinfowler.com/articles/practical-test-pyramid.html [dated]
- The cost of E2E tests: https://testing.googleblog.com/2015/04/just-say-no-to-more-end-to-end-tests.html [dated]
- Add automated a11y checks and a few E2E tests of critical journeys, and treat RUM as the final test lane. Lane design in general belongs to `test-lanes-and-coverage` if available.

## 6. Platform-feature gate

Before depending on a browser feature, check its Baseline status. "Newly available" means it is
supported in all core browsers; "widely available" means 30 months after that. Use this to decide
whether a polyfill or fallback is needed. https://web.dev/baseline ; https://web.dev/articles/baseline-and-polyfills
