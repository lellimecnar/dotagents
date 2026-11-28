# Physical hardware audit (server over SSH, read-only)

Use when the owner asks "what can this box handle / why isn't X detected" about PCIe cards, drives,
NVMe, GPUs or RAM. Inventory live first, research second, then explain.

## Inventory commands (batch into 3-4 SSH calls)

1. Board/CPU/RAM: `dmidecode -t system -t baseboard -t bios`; `lscpu` (sockets, NUMA);
   per-DIMM map: `dmidecode -t 17 | awk '/^\tSize/{s=$2$3} /^\tLocator/{print $2, s}'`
   (shows empty slots, mixed sizes, DIMMs-per-channel). `dmidecode -t 39` = PSU model/watts.
2. Slots: `dmidecode -t slot` (designation names which CPU feeds each slot, width, bus address,
   In Use/Available). Cross-check `/sys/bus/pci/slots/*/address`.
3. Devices + links: `lspci -nn` filtered of chipset noise, `lspci -tv` (root port per device;
   x4x4x4x4 bifurcation shows as 00:02.0/.1/.2/.3 root ports), and per device
   `lspci -s <bdf> -vv | grep -E 'LnkCap|LnkSta'` — `(downgraded)` width = seating/riser/bifurcation.
   A device with `[disabled]` regions and no driver in lsmod is present but unused.
4. Disks: `lsscsi -g`, `lsblk -d -o NAME,SIZE,MODEL,TRAN,ROTA`, `zpool status`, `zpool list -v`.
5. Enclosure bays (key for "drive not detected"): for each `/sys/class/enclosure/*/Slot NN`, read
   `status` and resolve `readlink -f Slot/device` -> `block`. **SES status `OK` with no block device
   = drive physically present and powered but never linked** — the exact bays to investigate.
   The expander model (`device/model`) identifies the backplane; `expander_add
   ... parent(...)` in the kernel log shows cascading.
6. Kernel evidence: `journalctl -k -b -g '<pattern>'` (always use `-g`/`-b`; an unfiltered grep over
   `-b -1` on a long-uptime host can exceed 90 s). `/proc/cmdline` for IOMMU.

## Interpretation rules

- Do the lane math per CPU from the board manual/QRG slot table before accepting "not enough
  lanes". On dual-socket boards RAM never consumes PCIe lanes; slots die when the CPU feeding them
  is absent, or devices drop with "PCI out of resources" when Above 4G Decoding is off (large-BAR
  cards such as Tesla). Filling the 3rd DIMM per channel lowers memory speed, not PCIe.
- NVMe on a multi-M.2 carrier needs slot bifurcation set in BIOS (x16 -> x4x4x4x4; x8 -> x4x4);
  only x16 slots take a 4-drive card.
- Present-but-unlinked SATA drives in hot-swap backplanes: follow "Drive present but not detected"
  below. PWDIS (3.3 V pin; Kapton over the first 3 power pins) is only a suspect when no drive of the
  same model works on that backplane — the same model running fine in another bay rules it out.
- An onboard hardware RAID controller hides disks unless configured as JBOD; for ZFS use an HBA in IT mode.
- Check the chassis' expansion-slot form factor before recommending any card: dense storage
  chassis are often low-profile only, and storage PDBs often
  lack PCIe GPU power leads. Say what you could not verify.
- GPU driver eras: older GPU generations drop out of current driver branches, and current inference
  runtimes may no longer support them. Check the vendor's driver support matrix for each card at task
  time; mixing generations on one host forces the oldest driver.
- ZFS layout review: a dedup vdev with `no DDT entries` (`zpool status -D`) and dedup off is wasted
  disks; L2ARC adds little with very large RAM; special vdevs on consumer NVMe are pool-critical.
  Removal of special/dedup vdevs is possible only with all-mirror top-level vdevs — propose, never run.

## Drive present but not detected (SAS expander backplane)

Order matters; each step is cheap and eliminates a whole class.

1. **Compare against the working drives first.** `lsscsi` shows model + firmware of every working
   disk. Drives the owner thinks are "all the same" are often two models or batches (different part
   numbers or firmware); if the failures all come from one batch, it is drive-side, not a controller "limit".
2. **Help the owner find the bay:** `echo 1 > "/sys/class/enclosure/<encl>/Slot NN/locate"` blinks
   the bay LED (harmless; turn off with 0 afterwards). SES `Slot NN` is 1-based, the mpt3sas log's
   `slot(N)` is 0-based, and printed tray labels may follow neither — trust the LED.
3. **Move the drive to a far, empty bay.** Failure that follows the drive = drive; failure that
   stays with the bay = bay/backplane. Do this before any software probing.
4. **Ask the expander, not sysfs.** With `smp-utils` + `sg3-utils` (the distribution's official packages;
   the owner approves the install): `smp_discover_list --brief /dev/bsg/expander-H:N` lists attached phys
   (uplinks show initiator addresses, drives `t(SATA)`); `sg_ses -p aes /dev/sgX` maps device slot
   -> expander SAS address (phy id = address offset; the slot-to-phy offset differs per backplane, so read it, never assume it).
   Then `smp_discover -p <phy>` (link rate, `sata_device=1`) and `smp_rep_phy_sata -p <phy>`:
   a healthy drive returns a register D2H FIS and an affiliated initiator; **link up +
   `sata_device=1` + "Phy does not support SATA" / no FIS = the drive spins and does OOB but never
   finishes SATA init** — a drive fault, not cabling. `smp_rep_phy_err_log -p` gives error counters.
   Linux `/sys/class/sas_phy/*/negotiated_linkrate` is a cached snapshot and goes stale after
   hot-plug; never conclude from it alone.
5. **Physical check in a powered bay:** ask the owner to feel/listen 20 s after insertion. Smooth
   whine = spins; beeping/clicking = stuck motor/heads. A 3.5" drive needs a dock with its own 12 V
   brick — 2.5"/optical enclosures and bus-powered adapters make healthy drives beep or show `0B`.
6. **Direct test only if still ambiguous:** plain motherboard SATA port in a desktop or the server.
   Some VM or container USB passthrough layers reject ATA passthrough (smartctl "Operation not
   permitted"): read SMART on a host with direct access.
7. Conclude with warranty route by serial (the vendor's written or web route) and confirm the
   remaining bays are usable.

## Report shape

Verified inventory table -> each owner hypothesis confirmed or refuted with the evidence -> concrete
findings (bay numbers, slot names) -> options (A/B/C, recommended first) -> numbered next steps with
time estimates, the first needing the owner's physical input. State plainly that nothing was changed.
