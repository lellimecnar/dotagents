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
- **A manifest-only uv bump can leave frozen consumers on the old version.** For example, a
  Dependabot `pip` entry can update `pyproject.toml` without refreshing `uv.lock`; Dependabot has a
  separate `uv` ecosystem. Plain `uv sync` can update a stale lock and test a different resolution
  from a build using `uv sync --frozen`, which uses the existing lock without a freshness check
  ([uv locking and syncing](https://docs.astral.sh/uv/concepts/projects/sync/)). Check the untouched
  head with `uv lock --check` or the applicable command with `--locked`, and inspect resolved
  versions even if the check passes: a constraint change can leave the old pin valid. Regenerate a
  stale lock with `uv lock`; if a specific upgrade is intended and the old pin is retained, use
  `uv lock --upgrade-package 'name==version'` with the intended, allowed version. Check the direct
  and transitive version diff, then use the main skill's lockfile reconciliation and approval steps
  for any authorized push and re-verdict.
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
  toolchain download cannot hide it. Use [Go's published release policy](https://go.dev/doc/devel/release#policy) for
  the support check in the main skill's toolchain-floor Hold procedure.
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
- **New Python interpreter:** check the lock for the target, then check the pins selected for each
  target platform for compatible wheels on the configured index. Match interpreter, ABI (including
  compatible `abi3` wheels), OS, libc and architecture; a matching `cpXY` prefix alone is not enough.
  To verify a published wheel can actually be installed, use a fresh disposable environment on the
  target and run `uv pip install --python <venv-python> --no-cache --only-binary :all: --no-deps 'name==version'`
  for the exact pin. A dry run is only a resolution check; a warm cache can hide a prior source build.
  If no compatible wheel is available, check whether an sdist exists and whether the actual build
  stage has the toolchain and libraries to build it.
- **Build proof is not run proof.** A successful `docker buildx build --load` proves that the
  tested target builds and loads. Dockerfile `RUN` steps may exercise parts of the image, but
  `--load` does not execute the resulting image's entrypoint. Even for a static binary on a minimal
  base, run the resulting image for claims about startup on the target architecture or TLS trust, and
  report the build and runtime checks separately.
- **Images no CI job builds** (for example, a dev container omitted from the build matrix):
  check the jobs and their conclusions at the reviewed head. A green or skipped rollup does not
  establish image coverage; a workflow excluded by path filters can also leave a required check
  pending. Build the affected target with the project's real context, arguments and platforms when
  possible. When you cannot, inspect the registry manifest or index for the required OS,
  architectures and variants, and check each relevant platform's image config (`User`, `Env`/`PATH`,
  `WorkingDir`) against what the Dockerfile assumes. Check OS packages in the target distro suite,
  add-on installer support and sibling pins. Report that inspection separately; the build is
  unverified, and any verdict that requires build evidence must wait for it.
- **An unpinned Python dependency floor does not describe the resolved change.** If CI installs
  without an enforced lock or equivalent pins, it may select a newer compatible release or change
  transitive versions; it may also select the same versions on base and head. Recreate the CI
  install in separate clean environments at the base and reviewed head, with the same interpreter,
  platform, index and resolver settings. Record both resolved sets and compare the affected
  project's type checks and tests before attributing a failure to the bump.

## Docker ignore rules

Dependabot strips the registry host from image references, so `registry.example.com/team/image:tag`
matches as `team/image`. Validate every `dependency-name` against the bot's parsed name (its
updated-dependency metadata), not the literal `FROM`. A green config check proves schema only, not
that the ignore selects the image.

For Dependabot Docker version updates, match `ignore.update-types` against the version parsed
from the tag. For example, `python:3.12` -> `3.15` and `alpine:3.20` -> `3.24` change the second
component without changing the first, so `version-update:semver-minor` matches them;
`version-update:semver-major` does not. Check how the bot parses nonstandard tags before choosing
an update type. These update-type ignores do not apply to security-only updates.

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
