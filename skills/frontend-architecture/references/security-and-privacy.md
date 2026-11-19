# Security and privacy boundaries (step 5)

Depth for step 5 of the SKILL.md sequence. Each principle gives the value it protects, how to
spot a violation, and when not to apply it. Make the decisions before delivery and platform
choices are locked in, because session transport, third parties and storage rules constrain
rendering, hosting and state. Standards and guidance change: look up the current versions at task
time and record the date checked. Sources were retrieved 2026-11-19. Threat-model method in
general: https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html

## 1. Principles

### Classify browser-held data and mark trust boundaries
- **Value:** you know what an XSS, a stolen device, a shared computer or a malicious extension
  can reach, and which controls each data class needs.
- **How:** list every data class the client receives, renders, caches or stores (credentials,
  personal data, payment data, internal content, public content). Draw the boundaries: browser,
  first-party origin(s), BFF or API, third-party origins, service worker, native shell. Anything
  in the browser is readable and modifiable by the user and by script running in the page.
- **Spot:** no data inventory; the API returns whole records and the UI filters them; a security
  review that starts at the API gateway.
- **When not:** a fully public, static, anonymous site with no forms still needs headers and
  third-party control, but not a data-class inventory.

### Choose session or token transport, then the matching CSRF defence
- **Value:** credentials stay out of reach of injected script, and state-changing requests
  cannot be forged cross-site.
- **How:** default for a first-party web app: a server-held session (or tokens held by a BFF)
  referenced by a `Secure`, `HttpOnly`, `SameSite` cookie, scoped narrowly. Cookie-authenticated
  writes then need a CSRF defence (framework token or equivalent, plus origin/fetch-metadata
  checks) per the current OWASP guidance; `SameSite` alone is defence in depth, not the whole
  defence. Bearer tokens in script-readable storage avoid CSRF but expose the token to any XSS.
  For OAuth/OIDC in a browser, follow the current browser-based-apps guidance (it describes the
  BFF, token-mediating backend and browser-only patterns and their risks).
- **Spot:** long-lived access or refresh tokens in `localStorage`; cookie-authenticated `POST`
  endpoints with no CSRF control; session identifiers in URLs; no server-side logout or
  revocation.
- **When not:** native or third-party clients that cannot use cookies; use short-lived,
  audience-restricted tokens and the platform's secure storage instead.
- Sources: https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html ;
  https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html ;
  https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/Cookies ;
  https://www.rfc-editor.org/rfc/rfc10017.html (OAuth 2.0 for Browser-Based Applications) ;
  https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html

### The server owns authorization
- **Value:** hiding a control is not access control; the server decision is the only one an
  attacker cannot edit.
- **How:** for each protected action and resource, name the server layer that decides (API,
  BFF, domain service) and the policy source. The UI may mirror permissions for usability only.
  Server components and server actions are server endpoints and need the same checks.
- **Spot:** role checks only in client code; object IDs accepted without an ownership check;
  over-fetching filtered in the browser.
- **When not:** never skipped where data is not public; the backend design is owned by
  `backend-and-data-architecture`.
- Source: https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html

### Govern third-party scripts
- **Value:** every third-party script runs with your origin's privileges and sees your users'
  data.
- **How:** keep an inventory with owner, purpose, data received and removal date. Prefer
  server-side or sandboxed (iframe, separate origin) integration; pin and integrity-check static
  third-party files (Subresource Integrity); constrain with CSP; review tag-manager changes like
  code. Count third-party bytes against the performance budget.
- **Spot:** a tag manager editable outside review; scripts nobody can name an owner for; CSP
  widened for a vendor.
- **When not:** none for production; a prototype without user data can defer the inventory.
- Sources: https://cheatsheetseries.owasp.org/cheatsheets/Third_Party_Javascript_Management_Cheat_Sheet.html ;
  https://developer.mozilla.org/en-US/docs/Web/Security/Defenses/Subresource_Integrity

### Privacy, consent and analytics by design
- **Value:** collect only what a stated purpose needs, and honour consent before collection, not
  after.
- **How:** decide the analytics and RUM scope per purpose; gate non-essential collection on
  consent where the law requires it; strip personal data and secrets from telemetry, URLs and
  error reports; set a `Referrer-Policy`. Confirm the legal requirements where the product ships
  at task time.
- **Spot:** analytics loaded before consent; full URLs with emails or IDs in third-party
  analytics; session replay with no masking.
- **When not:** strictly necessary processing still needs minimisation, though not consent.
- Sources: https://cheatsheetseries.owasp.org/cheatsheets/User_Privacy_Protection_Cheat_Sheet.html ;
  https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Referrer-Policy

### Sensitive data in browser storage and caches
- **Value:** limits what XSS, shared devices and forensic recovery can extract.
- **How:** by data class, decide what may live in `localStorage`, IndexedDB, Cache Storage,
  service-worker caches and the HTTP cache; set `Cache-Control` on authenticated responses; clear
  client stores on logout; encrypt or avoid local copies of sensitive data in local-first and
  offline designs.
- **Spot:** authenticated API responses cached by the service worker; personal data persisting
  after logout.
- **When not:** public, non-personal content can be cached freely.
- Source: https://cheatsheetseries.owasp.org/cheatsheets/HTML5_Security_Cheat_Sheet.html

### Upload and download handling
- **Value:** user files cannot become stored XSS, malware delivery or resource exhaustion.
- **How:** validate type and size on the server, rename, store outside the web root or on a
  separate origin, serve user content with `Content-Disposition: attachment` or a sandboxing CSP,
  and scan where the risk warrants it. Client-side checks are usability only.
- **Spot:** uploads served from the app origin inline; type checked only by extension in the
  browser.
- **When not:** no user-supplied files.
- Source: https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html

### Abuse controls
- **Value:** sign-up, login, search, messaging and expensive endpoints are not free for bots and
  attackers to exhaust.
- **How:** write abuse cases for each public flow; place rate limits, quotas and bot friction on
  the server or edge; design UI for throttling and lockout states.
- **Spot:** client-side-only throttling; no limit on password reset or invitation sends.
- **When not:** internal tools behind strong authentication may need only basic limits.
- Sources: https://cheatsheetseries.owasp.org/cheatsheets/Abuse_Case_Cheat_Sheet.html ;
  https://cheatsheetseries.owasp.org/cheatsheets/Denial_of_Service_Cheat_Sheet.html

## 2. Map to OWASP ASVS (method)

1. Open the current ASVS release at task time: https://owasp.org/www-project-application-security-verification-standard/
   (versioned source: https://github.com/OWASP/ASVS).
2. Choose the level from application risk, data sensitivity and required assurance, not team
   size. In ASVS 5.0.0, Level 2 is described as the level "most applications should be striving"
   for: https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x03-What-is-the-ASVS.md
3. Select the chapters the design touches. **[dated: 5.0.0 layout]** includes Web Frontend
   Security, File Handling, Authentication, Session Management, Authorization, Self-contained
   Tokens, OAuth and OIDC, and Data Protection. Chapter names and numbers can change between
   versions, so map by topic.
4. For each applicable requirement record: the design decision that satisfies it, the gate that
   verifies it, or an explicit risk acceptance with an owner.

## 3. Gates

| Gate | Value protected | Measure | Enforce where |
|---|---|---|---|
| Cookie attributes | Session confidentiality | Session cookies are `Secure`, `HttpOnly`, `SameSite`, narrowly scoped | Response-header test in CI or at deploy |
| CSRF on state-changing requests | Request integrity | Cross-origin forged write is rejected | Integration test per write endpoint class |
| No tokens in script-readable storage | Credential exposure under XSS | Lint/grep rule plus a browser test inspecting storage after login | CI |
| Server-side authorization | Access control | Requests as a lower-privileged or other user are denied | API/integration tests per protected resource |
| Third-party inventory and integrity | Supply chain and privacy | Every external script listed with owner; SRI where static; CSP matches inventory | CI check against inventory; review |
| Consent before collection | Privacy compliance | No non-essential network calls before consent | Browser test on a clean profile |
| Sensitive caching | Data at rest in the browser | Authenticated responses carry `Cache-Control: no-store` or a justified policy; stores cleared on logout | Header test plus browser test |
| Upload handling | Stored XSS and malware | User files served as attachment or from a sandboxed origin; type and size rejected server-side | Integration test |
| Abuse limits | Availability and cost | Limits trigger on public flows | Load or integration test |
| ASVS traceability | Assurance | Each selected requirement maps to a decision, gate or accepted risk | Review checklist |

XSS output encoding, strict CSP, Trusted Types and dependency supply-chain gates are in
[budgets-and-quality-gates.md](budgets-and-quality-gates.md) §3.
