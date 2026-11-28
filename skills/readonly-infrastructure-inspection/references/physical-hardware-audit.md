# Physical hardware audit (server over SSH, read-only)

Use when the owner asks "what can this box handle / why isn't X detected" about PCIe cards, drives,
NVMe, GPUs or RAM. Inventory live first, research second, then explain.

## Inventory commands (batch into 3-4 SSH calls)

1. Board/CPU/RAM: `dmidecode -t system -t baseboard -t bios`; `lscpu` (sockets, NUMA).
   Use `dmidecode -t 17` for each DIMM's full locator, bank locator, size, and configured speed;
   use the board's population table to map locators to channels. `dmidecode -t 39` can supply PSU
   model and rated capacity when populated; verify against the management controller or PSU label
   if the firmware data is absent or unclear. Redact serials and UUIDs in shared output.
2. Slots: `dmidecode -t slot` for firmware-reported designation, width, usage, and bus address.
   Cross-check `/sys/bus/pci/slots/*/address` where available and consult the board/riser manual
   for CPU ownership and slot wiring.
3. Devices + links: use `lspci -nn` for inventory and `lspci -tv` to trace endpoints through bridges
   to their root ports. Compare `LnkCap` and `LnkSta` at both ends using `lspci -s <bdf> -vv`;
   compare negotiated width against both ends' capabilities and the electrically wired lanes. If
   it is unexpectedly narrow, check lane sharing, bifurcation, seating, and the riser. Use
   `lspci -s <bdf> -k` and its `Kernel driver in use` field to check binding; disabled BARs and
   `lsmod` alone do not establish whether a device is in use.
4. Disks: `lsscsi -g`, `lsblk -d -o NAME,SIZE,MODEL,TRAN,ROTA`, `zpool status`, `zpool list -v`.
5. Enclosure bays: under `/sys/class/enclosure/`, enumerate reported bay components (names vary).
   Read each component's `slot` and `status` where exposed; follow its `device` symlink if present
   and check `device/block/`. `OK` without a block mapping is a lead, not proof of drive presence,
   power, or link state. Correlate SES data with the controller's device and link inventory. Record
   the SES device model as an identification clue; verify the backplane part and expander topology
   separately from the hardware inventory and controller or kernel topology data.
6. Kernel evidence: on systemd hosts, use `journalctl -k -b -g '<pattern>'` for the current boot;
   select the relevant boot and narrow with `--since` or `--until` around the event. Otherwise use
   the host's kernel log source. `/proc/cmdline` shows requested IOMMU options; check initialization
   messages and the applicable devices' `iommu_group` links for runtime evidence.

## Interpretation rules

- Calculate lanes from the board and riser slot tables for the installed CPUs. An absent CPU can
  leave its wired slots unavailable. Conventional DIMMs occupy memory channels, not PCIe lanes;
  check allowed DIMM counts and speed by CPU and population in the memory table. For BAR allocation
  failures, check firmware MMIO resources and Above 4G Decoding support before blaming lane count.
- A passive multi-M.2 NVMe carrier needs host bifurcation matching the card wiring for all drives
  to enumerate (e.g. x4/x4/x4/x4 for four x4 drives, x4/x4 for two). A switch-based carrier does not
  need host bifurcation; check its supported upstream width and physical fit, and account for the
  drives sharing that upstream bandwidth.
- For SATA drives that do not spin up, check whether the exact drive part number implements
  Power Disable (PWDIS) on SATA power pin P3 and whether the suspect bay drives P3 high. A drive
  with the same model label working in another bay does not exclude either difference. Use a
  vendor-supported compatible power path for any operator-run test; do not modify drive contacts.
- For ZFS, verify that the host sees individual disks, stable identifiers, and device health
  information. Prefer direct attachment or an HBA; use supported passthrough/JBOD on a RAID-capable
  controller if it exposes the real disks. IT firmware is an option on controllers that support it,
  not a requirement for all HBAs.
- Check the chassis' expansion-slot form factor before recommending any card: dense storage
  chassis are often low-profile only, and storage PDBs often
  lack PCIe GPU power leads. Say what you could not verify.
- GPU compatibility: check the vendor's current supported-products lists and the workload's runtime
  requirements for each card. If the cards share one driver stack, find a supported driver branch
  that covers every card and the required runtime; if none matches, report the incompatibility.
- ZFS layout review: compare `zpool status -D`, `zpool list -v`, and effective dataset dedup
  settings before judging a dedicated dedup vdev. Use `arcstat` during representative workloads to
  check whether L2ARC serves reads that miss ARC. A special vdev holds pool-critical allocations
  regardless of drive model; assess its redundancy. Before proposing top-level special/dedup vdev
  removal, check the installed OpenZFS `zpool-remove(8)` rules, including device-removal support,
  primary topology (RAIDZ/dRAID exclusion), matching `ashift`, loaded encryption keys, and room to
  evacuate. Do not run the removal during an audit.

## Drive present but not detected (SAS expander backplane)

Gather read-only evidence before proposing a physical test. Treat each result as a clue until
independent checks agree.

1. **Compare with working drives first.** Start with the model and revision reported by `lsscsi`
   for visible SCSI devices, then verify exact part numbers and firmware via labels or direct device
   identification. If failures cluster by batch, prioritize drive and compatibility checks, but
   first rule out the batches being grouped by enclosure, power path, or controller.
2. **Identify the physical bay.** Match the enclosure and its reported slot to the chassis bay
   label; do not assume a fixed offset between SES, driver logs, and tray numbering. If needed, ask
   the operator to use the enclosure's locate LED, confirm it at the chassis, and restore its prior
   state. A locate indication identifies a bay; it is not clearance to remove a disk.
3. **Plan a controlled bay swap only if needed.** After the read-only checks in step 4, the
   operator verifies the drive identity and that removal is safe for the pool, then tests in a
   known-good empty bay of the same interface. Follow the hardware's hot-swap procedure or power
   down. If the symptom follows the drive, investigate the drive or carrier; if it stays with
   the original bay, investigate that bay's power, link, and upstream path.
4. **Correlate live enclosure and expander data.** If `smp_utils` and `sg3_utils` are available,
   identify the SES generic device and the expander's SMP target. Run `sg_ses --readonly --join /dev/sgX`
   for enclosure slot descriptors and SAS data, and `smp_discover_list --summary --dsn /dev/bsg/expander-H:N`
   for the expander's PHY inventory and any reported slot numbers, if supported. Match that slot
   number or cross-check the attached address and topology against the enclosure map; do not
   compute a PHY ID from an address or assume a constant slot offset. On the mapped PHY, use
   `smp_discover -p <phy> /dev/bsg/expander-H:N` for current link and protocol data and
   `smp_rep_phy_err_log -p <phy> /dev/bsg/expander-H:N` for error counters. For an attached SATA
   device, `smp_rep_phy_sata -p <phy> /dev/bsg/expander-H:N` reports the available SATA FIS and
   affiliation information. Missing affiliation, a missing FIS, or a rejected request alone does
   not distinguish a drive fault from a mapping, link, power, or controller problem. Cross-check
   the driver's cached `negotiated_linkrate` against live data; after hot-plug it can lag.
5. **Observe spin-up for rotating drives.** A steady whir or vibration suggests rotation; repeated
   clicking or beeping is a symptom, not a mechanical diagnosis. Verify the manufacturer's power
   and spin-up requirements. A 3.5-inch SATA HDD generally needs a supply that provides both 5 V
   and 12 V with adequate spin-up current; a bus-powered adapter may not provide it.
6. **Propose a direct test only if still ambiguous.** Use a known-good motherboard SATA port and
   power supply for a SATA drive, or a compatible SAS HBA and power path for a SAS drive. If a USB
   bridge or virtualization layer blocks SMART passthrough, read SMART from a host with direct access.
7. If the evidence points to a failed drive, give the vendor's warranty lookup route; keep serial
   numbers out of shared reports. Say which bays were verified and which were not tested.

## Report shape

Give the verified inventory, then label each hypothesis supported, ruled out, or unresolved and cite
its evidence. Name the affected bays or slots, rank viable options, and distinguish read-only checks
from operator actions. Report any actions taken and their effects; say nothing changed only if true.
