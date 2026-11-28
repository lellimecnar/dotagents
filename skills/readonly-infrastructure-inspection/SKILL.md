---
name: readonly-infrastructure-inspection
description: Use when inspecting servers, storage, backups or networks with read-only keys.
metadata:
  version: "0.2.0"
  status: draft
  owner: maintainer
---

# Read-only infrastructure inspection

How an operator agent with read-only (auditor-scoped) API credentials answers "is everything OK?"
questions about a hypervisor, storage, backups, or a network controller, and reports plainly. The
same frame fits any infrastructure API you can read but not change.

## When to use

The owner asks about servers, VMs, containers, disks, storage, backups, resource use, the internet
connection, Wi-Fi, network devices, outages or ISP performance, and you hold read-only keys for the
relevant controller.

## Principles

- **Read-only by design, and by intent.** The credential should be scoped so the system itself
  refuses changes (an auditor role, a read-only API key). Even so, never *attempt* a change: no
  start/stop/reboot, create/delete, config edits, backups, migrations or setting changes. Start
  read-only until the owner explicitly grants write access in a separate, deliberate step.
- **When a change is wanted, describe it.** Give the exact steps (where to click or which command)
  and let the owner do it.
- **Credentials stay invisible.** Load them from the profile's secret file into the environment
  without echoing (`set -a; . <secret-file>; set +a`), and never print headers or tokens. Prefer
  sending headers via a file or stdin over argv.
- **Start broad, then drill.** One overview call usually answers most questions; drill into a node,
  guest, disk or device only where the overview shows trouble.
- **Report what needs attention, not raw JSON.** Plain words, worst first.

## Procedure

1. Load credentials silently; confirm the base URL is set.
2. Call the overview endpoint (cluster resources; sites/hosts/devices).
3. Drill down where the overview flags something: per-node status, per-guest status, storage pools,
   physical disks and SMART health, recent failed tasks, backup jobs; device lists, ISP metrics.
4. Follow pagination tokens until the list is complete before counting anything.
5. Report: what is down or offline; what is near full (a sensible default threshold is >85%); failed
   backups or tasks; disks with bad SMART health; firmware updates waiting; ISP outages, latency or
   packet loss; client counts if asked. Then, if the owner wants something changed, the exact steps.

Endpoint maps for two common systems (Proxmox VE and the UniFi Site Manager cloud API) are in
[references/api-maps.md](references/api-maps.md).

For physical hardware questions over SSH (PCIe lanes, slots, bifurcation, drive bays not
detecting, NVMe, GPUs, DIMM population, ZFS vdev layout), follow
[references/physical-hardware-audit.md](references/physical-hardware-audit.md): inventory live,
map enclosure bays to block devices, test each of the owner's hypotheses against evidence before
researching options.

## Pitfalls

- Self-signed certificates on homelab hypervisors often need `curl -k`; that weakens transport
  verification, so only use it against an address you know, and prefer pinning the CA when possible.
- A 403 on a write-shaped path is the system working as intended; don't retry or look for another
  route.
- "Near full" depends on the pool type: thin-provisioned storage can report usage that differs from
  what guests see. Say which number you report.
- SSH privileges do not expand the audit's authority. Keep the session read-only: propose a
  locate LED change to identify a bay, but let the operator perform it. Treat a SCSI rescan, an
  expander PHY reset, or a package install as a separate, explicitly authorized operation; a
  reset can interrupt live storage I/O.
- Report an incidental finding once with a proposed fix. If it is deferred, do not repeat it
  unless new evidence changes its severity or it affects the current diagnosis or next action.

## Verification

Every claim in the report traces to a call made this session; pagination exhausted; no credential
value appears in output; no write method was used.

## Project specifics

The local skill supplies the secret-file paths, base URLs, node and site names, the scope of each
key, and the owner's standing rules about when write access may be granted.
