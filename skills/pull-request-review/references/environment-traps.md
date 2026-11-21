# Review-environment traps

Failures that come from the reviewer's environment rather than the PR. Each looks like a PR defect
on first sight. Rule them out before attributing a red, and keep the exact CI command unmodified as
the primary gate: the workarounds below produce evidence, they do not replace that gate. Tool names
are examples of public ecosystems where the trap is common; the pattern carries over.

## Temp directories

- **Temp dir inside a repository.** Tests that check "not under a repository" (repo discovery,
  workspace-root detection) pass in CI but fail when the test temp dir sits inside a checkout or a
  directory with a workspace marker, because the fixture inherits it. Keep the runner's temp root
  outside any repository (pytest example: `TMPDIR` or `--basetemp`) and rerun those cases there.
- A small or `noexec` temp dir: see the Pitfalls in the main skill.

## Lockfiles that pin per-platform hashes

- **The lock lacks hashes for your platform.** Some lockfiles record checksums per OS/architecture
  (Terraform example: `.terraform.lock.hcl` provider hashes). Validation on a reviewer platform the
  lock does not cover rejects the installed providers. Prefer a platform already covered by the lock for the primary gate.
  To diagnose an uncovered platform, use a disposable copy and the ecosystem's supported checksum
  workflow (for example, `terraform providers lock` for the target platform). Verify the package
  source and unchanged versions; if the platform is required, report the committed lockfile gap. Never modify the PR's
  lock, and never plan or apply against live infrastructure as part of a review.

## Policy guards in infrastructure code

- **Exercise a policy regression guard offline.** For a guard meant to stop a permission policy from
  regressing (for example an IAM deny that must not be lost), test the rendered policy and the guard
  with provider-free fixtures or the tool's native mocked tests. Confirm the guard *fails* when the
  deny is removed or a wildcard covers an action it must exclude. Check the guard's severity too: a
  Terraform `check` block only warns during plan/apply and blocks nothing. This is offline evidence,
  not a substitute for an authorised plan against the real environment.

## CPU limits

- **Reported CPUs exceed the container's quota.** On quota-limited hosts `nproc` (and a test
  runner's automatic worker count) can be far above the cgroup CPU allowance, so a parallel suite
  thrashes or stalls. If a suite makes no progress with automatic workers, read the quota and rerun
  the full suite with one worker and a progress reporter (Vitest example: `--maxWorkers=1`) before
  attributing a timeout to the PR.

## Native addons

- **Install under the runtime the tests use.** Native addons are built for one runtime ABI. Install
  workspace dependencies under the same runtime version the tests run with; an ABI mismatch (Node
  example: a `NODE_MODULE_VERSION` error) needs a matching runtime or a native rebuild, not a
  dependency or test change.
