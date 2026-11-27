# Read-only bank data for an agent (choosing and configuring)

US household context. Vendor terms change; verify them at decision time.

## Principles
- **Order of preference:** an official read-only API or MCP server from the provider the household
  already uses > community open source, self-hosted > forks > a custom build on a developer
  aggregator.
- **Scope to read only.** Use a dedicated viewer-role member or an API key granted only read
  scopes. Owner-level OAuth often still exposes transfer or rule-trigger tools that create
  approval-pending proposals; even a proposal violates "never moves money".
- **Secrets:** setup tokens and access URLs that embed credentials are bearer secrets; store them in
  the vault, never in config files or chat.
- **Data gaps to expect:** linked external accounts lag the bank sync; history windows can be
  ~90 days; most providers have no native "bill" object, so take due dates from statements,
  creditor portals or bill notices, or keep a bill list from them. Posting history shows only
  when a payment historically posted, never a due date.
- **Avoid** unofficial, credential-based APIs (email + password + MFA secret through a
  reverse-engineered API) and stale forks superseded by an official server.

## Vetting a current option
Options and their terms change; search for current ones at decision time (the provider the
household already uses first, then open-source self-hosted tools and their MCP servers, then
developer aggregators) and judge each on:

| Criterion | What to check |
|---|---|
| Source | Official API or protocol from the provider, community open source, fork, or unofficial reverse-engineered API (avoid the last) |
| Read-only scope | Can access be limited to reading (viewer role, read scopes, server started without a write flag)? Is the token unscoped? |
| Credentials | Token or OAuth stored in the vault vs. email + password + MFA secret (avoid) |
| Data | Update frequency, history depth, whether linked external accounts lag, whether bills/recurring items exist as objects |
| Maintenance | Recent commits and releases, open issues, whether an official server has superseded it |
| Cost and hosting | Current price from the vendor's own page (dated), and whether it needs a host to run |
| Adoption cost | Does it require the household to switch budgeting tools? |

Record the options compared, the date checked and the source links in the answer, not in this
skill.

Example categories to search: a budgeting/banking provider's official API or remote MCP server;
read-only aggregation protocols (e.g. SimpleFIN); self-hosted budgeting apps with MCP servers
(e.g. Actual Budget, Firefly III); budgeting-service APIs (e.g. YNAB); developer aggregators (e.g.
Plaid, Teller), which mean a custom build.
