# Ecosystem traps for dependency reviews

Public tool behaviour that repeatedly decides dependency verdicts. Confirm against the live
upstream source when in doubt; dates and floors move.

## Lockfiles and resolvers

- Regenerate per ecosystem: pnpm `pnpm install --lockfile-only`; uv `uv lock` in the project dir;
  Go `GOWORK=off go mod tidy` in the module dir; Terraform
  `terraform providers lock -platform=<each platform you build on>`; GitHub Actions have no lock.
- **uv:** a lock that only a newer uv can read breaks CI and image builds that pin an older uv.
  Inventory every uv version pinned by CI workflows and image builds, and exercise each frozen
  consumer command (`uv sync --frozen`, `uv export --frozen --no-dev`) with the oldest applicable
  pin, plus `uv lock --check`. Never infer incompatibility from the `revision` header alone. For a
  floor change, classify "expanded resolution" from a programmatic version-only lock diff; a format
  or root-specifier edit with no package-version change is not expansion.
- **pytest warning counts:** the final "N warnings" summary can omit warnings emitted before
  collection; inventory every warning line before claiming a count or sole source.
- **CLI-only packages:** smoke the packaged non-mutating entry point (`--help`) instead of inventing
  an HTTP health probe.

## Go

- Go has no dev-dependency section. Never infer runtime scope from a direct `go.mod` requirement:
  if every import is in `*_test.go`, the package is test-only.
- Reconcile with `GOWORK=off` to isolate the module's canonical `go.mod`/`go.sum`, then also run a
  module-local image-equivalent build (`GOWORK=off go mod download && GOWORK=off CGO_ENABLED=0 go
  build ./...`): a workspace build can pass while the image build fails. Then run the real CI
  commands with workspace mode on; if they change `go.work.sum`, reproduce on the base and compare
  entries semantically, not by line count.
- A `go` directive raised by a dependency breaks `go.work` ("requires go >= …"). That is a Hold for a
  toolchain decision, not a lock fix. Build the old floor with `GOTOOLCHAIN=local` so automatic
  toolchain download cannot hide it. Check Go's published release policy for which releases are still supported: if the repo's
  pin is no longer supported, the toolchain move is the unblock (see the toolchain-floor rule in the main skill).
- Before prescribing pins or ignores, intersect the direct requirements (`go mod edit -json`) with
  `go list -m -json all` `GoVersion` values and name *every* bumped direct module at the new floor.
  Without a toolchain, read a module's floor from `https://proxy.golang.org/<module>/@v/<version>.mod`
  (first `go ` line), and check every intermediate release before writing "pin below X".
- Enumerate the migration surface by grep, not memory: Dockerfile base tags, `go-version` in every
  workflow, `go.work`, every `go.mod` including nested ones.
- `staticcheck` SA1019 turns a deprecation into a red lint job: a deprecation is a blocker.

## Runtimes and base images

- Node/Go/Python moves are deliberate repo-wide changes. Reject a bump that moves one image or
  directive while the repo pins (`.nvmrc`, CI versions, sibling Dockerfiles) stay put.
- Check LTS status against the live release schedule (Node's `schedule.json`) before citing
  "non-LTS": an even Node major becomes LTS in late October.

## Docker ignore rules

Dependabot strips the registry host from image references, so `registry.example.com/team/image:tag`
matches as `team/image`. Validate every `dependency-name` against the bot's parsed name (its
updated-dependency metadata), not the literal `FROM`. A green config check proves schema only, not
that the ignore selects the image.

## CI action majors

- Node 24-based actions need a sufficiently new runner (2.327.1+ at time of writing); if the same
  runners already run other node24 actions, the floor is met.
- Count every changed action occurrence from the exact diff across all workflows, with file:line,
  and separate distinct configurations. Push- and deploy-only call sites are part of the upgrade; a
  green PR run does not exercise them.
- Setup actions whose majors change bootstrap behaviour cannot be proven without real runs of each
  distinct setup path: Hold until they run.
- When PR CI never reaches a bumped action (dispatch- or push-only workflow, disabled or skipped
  job, failure-only step), prove the bump from the call sites instead:
  1. Find every occurrence across the whole tree at the head, not only the edited files.
  2. Diff the action's definition (its metadata file) at the old and new tags; removed inputs and
     outputs only show in that comparison.
  3. Read each call site for consumed outputs (a step id used later), not only the inputs it passes.
  4. Check that defaults the workflow relies on did not change.
  5. Prove a shared runtime floor once, from an action that did run at that head.

## OpenTelemetry (Python)

Lock expansions cross two upstream release streams: read every intervening stable release in
`opentelemetry-python` and every matching beta release in `opentelemetry-python-contrib`. The direct
exporter's release page omits changes pulled through SDK, API, semantic conventions and
instrumentation. Grep each breaking item's class/method (including subclasses) in the service, and
verify collector/exporter versions still agree with the deployed collector.

## Static analysis upgrades

When a type checker changes parser behaviour, scan for the exact statement shapes affected. Example
(mypy): type comments on `for`/`with` statements can be silently ignored by a new parser, while
`# type: ignore[...]` is unaffected; scan with a statement-shaped pattern such as
`rg -n '^\s*(async\s+)?(for|with)\b[^#]*#\s*type:'` and inspect every hit.

## Changelogs

Release-automation projects (e.g. Release Please) can keep the only complete notes in the release PR
body while GitHub Releases and `CHANGELOG.md` are empty. Use the tag comparison's commit subjects to
find the release PR. Do not cite a *server product's* release page with the same version number as a
client library's notes.
