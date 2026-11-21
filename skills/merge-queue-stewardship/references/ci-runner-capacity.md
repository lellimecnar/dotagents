# CI runner capacity: when the queue is slow because CI is

Use when many PRs sit with *queued* (not failing) checks and merges stall. Field names and commands
below are GitHub Actions examples; other CI providers expose the same timestamps under other names.

## Diagnose from the forge API first

Agents usually have no host access to the runners, and the API answers most questions.

1. **Job timelines.** For recent runs, per job: queue wait is created → started (`created_at` →
   `started_at`), runtime is started → completed, and the runner name and labels say where it ran.
   Compare median and maximum wait: a low median with a huge maximum means stall episodes (bursts,
   a hung job), not steady overload.
2. **Cost per PR.** Count runner-minutes and jobs per PR run. Use the measurements to find expensive work that
   can be skipped safely. Keep required results reportable even when some jobs are inapplicable.
3. **Jobs that hit the platform default timeout** (hours, on most providers) each held a slot for
   that long. Set an explicit per-job timeout a little above the longest legitimate run.
4. **Burst fan-out.** Many PRs starting within minutes (dependency-bot rebases, sibling PRs), each
   fanning out many jobs. Fix it with the update bot's own grouping and open-PR limit settings
   rather than a custom throttle.
5. **Repo-wide re-triggers on unchanged heads.** Compare workflow, revision, attempt and triggering event.
   For example, bulk PR reopen events can fan out fresh runs without new PR commits. Verify the
   source of the burst from the timeline; limit refreshes to PRs needing fresh evidence and use
   the supported refresh path in the main skill's Pitfalls.
6. **Queued is not dead.** Before concluding "runner offline", check whether new job starts are
   still happening. Do not restart or cancel on queued jobs alone.
7. **Live hung jobs.** List the jobs in progress with their age and current step. A job running
   many times its normal duration, in the same step as an earlier hang, is holding a slot. If you are authorized, cancel
   that run (a cancel is reversible by re-running); otherwise hand it to whoever operates CI, name the runs and the reason in your report, and
   open a root-cause task for the step: a timeout bounds the damage but does not remove the hang.
8. **Permissions.** The runner-administration API often needs admin rights; a denied or not-found response does not
   establish runner health. Verify the endpoint and scope or report that administration data is unavailable.

## Splitting cheap gates from heavy CI

- A runner with extra labels still accepts jobs that ask only for its base labels, so adding a
  label to some runners reserves nothing. Partition with exclusive labels (heavy runners carry only
  `heavy`, light runners only `light`, as an example) and make every job's runner selector name one.
- Order matters: label the runners first, merge the selector change second. Merging first leaves
  every job queued with no runner able to take it. Keep the workflow PR unqueued until a runner with
  each label is confirmed online.
- Verify from the API afterwards: a heavy job lands on a heavy runner, and a light job starts promptly
  (no longer than its usual queue wait) while heavy runs are queued.
