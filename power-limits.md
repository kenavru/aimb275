# AIMB-275 power limits — PL1 / PL2 / PL3 / PL4 and RAPL

Standalone guide for **AIMB-275VG-00A5E** (Intel Q170, LGA1151, 12–24 V DC input).
It is based on the stock 16 MB SPI dump `stock1.bin`
(SHA-256 `aa2b4e33a046ee892c3b81ce3a8084d8b47bc4115f1737888959061385e4696a`):
the Setup IFR and the Setup default variable were extracted and decoded, so the
offsets and defaults below are from the firmware, not from a manual.

**TL;DR** — the BIOS already contains the full Intel power-management subtree
(PL1/PL2/PL3/PL4, Config TDP, MSR lock). Out of the box it programs **PL1 = CPU TDP**
and **PL2 = 1.25 × TDP**, and leaves the package power-limit MSR **unlocked**, so you can
change the limits from Linux without flashing anything. The board itself is a **65 W
design** — lowering is always safe, raising is the experiment.

---

## 1. What the firmware actually has

The power pages are not a stripped client menu. These questions exist in the IFR of
the stock image:

| Item | IFR QuestionId | Setup offset | Width | Default |
|---|---|---|---|---|
| `Power Limit 1 Override` | 0xDC | **0x4C9** | u8 | Disabled |
| `Power Limit 1` | 0xDD | **0x4C5** | u32, mW | 0 = program TDP |
| `Power Limit 1 Time Window` | 0xDE | **0x4CA** | u8, s | 0 = 1 s (desktop) |
| `Power Limit 2 Override` | 0xDF | **0x4CB** | u8 | **Enabled** |
| `Power Limit 2` | 0xE0 | **0x4CC** | u32, mW | 0 = 1.25 × TDP |
| `Package Power Limit MSR Lock` | 0xDB | **0x4DE** | u8 | **Disabled** (unlocked) |
| `Power Limit 3 Override` | 0x136 | 0x4D0 | u8 | Disabled |
| `Power Limit 3` | 0x137 | 0x4D1 | u32, mW | 0 = default |
| `Power Limit 3 Time Window` | 0x138 | 0x4D5 | u8, ms | 0 = default |
| `Power Limit 3 Lock` | 0x13A | 0x4D7 | u8 | Enabled |
| `Power Limit 4 Override` | 0x91 | 0x4D8 | u8 | Disabled |
| `Power Limit 4` | 0x92 | 0x4D9 | u32, mW | 0 = default |
| `Power Limit 4 Lock` | 0x93 | 0x4DD | u8 | — |
| `Platform PL1 Enable` | 0x8C | 0x4DF | u8 | Disabled |
| `Platform PL1 Power` | 0x8D | 0x4E0 | u32, mW | 0 |
| `Platform PL2 Enable` | 0x8F | 0x4E5 | u8 | — |
| `Platform PL2 Power` | 0x90 | 0x4E6 | u32, mW | 0 |
| `Config TDP Configurations` | form 0x2731 | 0x50C… | — | gated |
| `OverClocking Feature` | 0x2AA | 0x660 | u8 | Disabled |
| `Setup Item Hidden` | 0x9 | **0x1013** | u8 | 0 = hidden |

The setup variable is the standard AMI `Setup` varstore (`VarStoreId 0x1`), so plain
`setup_var <offset> <value>` targets it.

### Defaults, decoded

I read the Setup default blob directly (`NVRAM external defaults → StdDefaults →
Setup`), not just the help text:

```text
SpeedStep (0x4BE)          = 1     -> power pages are not suppressed
PL1 override (0x4C9)       = 0     -> BIOS programs TDP
PL1 value (0x4C5)          = 0
PL1 window (0x4CA)         = 0     -> 1 s desktop default
PL2 override (0x4CB)       = 1
PL2 value (0x4CC)          = 0     -> 1.25 x TDP
MSR lock (0x4DE)           = 0     -> PACKAGE_POWER_LIMIT is writable from the OS
PL3/PL4 overrides          = 0
OC feature (0x660)         = 0
Setup Item Hidden (0x1013) = 0
```

For a 65 W i5-8500 or i7-8700 that is **65 W sustained / ~81 W burst**, which is
exactly the board's thermal design point. For a 35 W T-series part it is 35 W / ~44 W.

## 2. Where the menu is

```text
Advanced
 └─ Power & Performance                      (form 0x2726)
     └─ CPU - Power Management Control       (form 0x2727)
         ├─ Boot performance mode
         ├─ Intel SpeedStep                   (0x4BE)
         ├─ Turbo Mode
         ├─ View/Configure Turbo Options      (form 0x2729)
         │   ├─ Power Limit 1 Override/Value/Window
         │   ├─ Power Limit 2 Override/Value
         │   ├─ Package Power Limit MSR Lock
         │   └─ Power Limit 4 Override/Value/Lock
         ├─ CPU VR Settings
         ├─ Platform PL1/PL2
         └─ Power Limit 3 Settings            (form 0x2730)
```

Visibility is gated by three things:

1. **SpeedStep** (`Intel(R) SpeedStep(tm)`, offset `0x4BE`). If it is disabled, every
   `PL1/PL2/PL4` item is suppressed (`SuppressIf 0x74 == 0`). Default is enabled.
2. **The dynamic CPU flag `0xC3F`.** `CPU - Power Management Control` is grayed out if
   `0xC3F == 1`. This is set by firmware, not by a setup item.
3. **`0xCE4`** (Config TDP). When it is 1, the PL1/PL2 override items are suppressed
   because Config TDP owns the limits; the Config TDP page becomes visible instead.

## 3. Unhiding suppressed pages

AMI has a global "hidden items" switch, exposed in the IFR as `Setup Item Hidden`
(QuestionId `0x9`, offset `0x1013`, inverted options: value 0 = hidden, 1 = visible).
Set it from a UEFI shell or GRUB with `grub-mod-setup_var`:

```text
setup_var 0x1013 0x1
```

Then reboot. This exposes the pages wrapped in `SuppressIf EqIdVal 0x9, 0`:
`OverClocking Performance Menu`, `Intel ICC`, `Thunderbolt(TM) Configuration`,
`Realsense 3D Camera Settings`, and others. It does **not** create new questions — it
only reveals what is already compiled in.

Caveats:

* Do **not** "Load Setup Defaults" afterwards — it writes `0x1013` back to 0.
* If `CPU - Power Management Control` is present but grayed out, the gate is `0xC3F`,
  not `0x1013`; use the runtime RAPL path instead of chasing the menu.
* Enabling `OverClocking Feature` (`0x660 = 1`) only reveals Processor/Memory/Uncore
  ratio and voltage pages. It is not needed for PL1/PL2.

## 4. Changing the 32-bit watt fields

`setup_var` writes a single byte. The PL1/PL2/PL3/PL4 **values** are 32-bit fields, so
either use a tool that writes dwords, or change them at runtime (next section).

* **RU.EFI** (UEFI shell): `UEFI Variables → Setup` → edit the dword at the offset
  from the table (little-endian, value in milliwatts). `Power Limit 1 = 45000` is
  `0x0000AFC8`, written as `C8 AF 00 00`.
* **GRUB `grub-mod-setup_var`**: recent builds provide wider helpers
  (`setup_var_2`, `setup_var_4`) next to the byte command; if yours only has the byte
  command, use RU.EFI.
* **Do not** write the value without also setting its `Override` byte to 1, or the
  BIOS will ignore it and program the default.
* Some items declare "reset required" (`Flags` bit for reset), so reboot after changing.

Example: 45 W sustained, 45 s window, 65 W burst on a 65 W CPU:

```text
0x4C9 = 1          # Power Limit 1 Override = Enabled
0x4C5 = 45000      # mW
0x4CA = 45         # seconds
0x4CB = 1          # Power Limit 2 Override = Enabled (already default)
0x4CC = 65000      # mW
0x4DE = 0          # keep the MSR unlocked
```

## 5. Runtime control (recommended): RAPL

The BIOS leaves `PACKAGE_POWER_LIMIT` (MSR `0x610`) unlocked
(`Package Power Limit MSR Lock = Disabled`), so Linux can program PL1/PL2 with
`intel-rapl`. This is instant, scriptable, and needs no firmware tools.

### Read

```bash
sudo modprobe msr
sudo rdmsr -a 0x610          # bit 63 = lock; 0 means writable

find /sys/class/powercap -name 'constraint_*_power_limit_uw'
cat /sys/class/powercap/intel-rapl/intel-rapl:0/constraint_0_power_limit_uw   # PL1, µW
cat /sys/class/powercap/intel-rapl/intel-rapl:0/constraint_1_power_limit_uw   # PL2, µW
cat /sys/class/powercap/intel-rapl/intel-rapl:0/constraint_0_time_window_us
```

On some kernels the path is `/sys/class/powercap/intel-rapl:0/` instead of the nested
one; use `find` to be sure. `constraint_0` is the long-term limit, `constraint_1`
the short-term one.

### Write

```bash
# 45 W sustained, 65 W burst, 28 s window
echo 45000000 | sudo tee /sys/class/powercap/intel-rapl/intel-rapl:0/constraint_0_power_limit_uw
echo 65000000 | sudo tee /sys/class/powercap/intel-rapl/intel-rapl:0/constraint_1_power_limit_uw
echo 28000000 | sudo tee /sys/class/powercap/intel-rapl/intel-rapl:0/constraint_0_time_window_us
```

Values are in **microwatts**; the window is in **microseconds**. Writing may fail with
`EACCES`/`EINVAL` if the MSR is locked by firmware, if the value is outside the CPU's
`PACKAGE_POWER_SKU` range, or if the kernel exposes the file read-only — check `rdmsr`
and `dmesg` first.

### Persist across reboot

`/usr/local/sbin/cpu-power-limit`:

```bash
#!/bin/sh
set -eu
d=$(dirname "$(find /sys/class/powercap -name constraint_0_power_limit_uw | head -1)")
echo "${1:-45000000}" > "$d/constraint_0_power_limit_uw"
[ -n "${2:-}" ] && echo "$2" > "$d/constraint_1_power_limit_uw"
```

`/etc/systemd/system/cpu-power-limit.service`:

```ini
[Unit]
Description=Intel RAPL package power limits
After=multi-user.target

[Service]
Type=oneshot
ExecStart=/usr/local/sbin/cpu-power-limit 45000000 65000000
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

```bash
sudo chmod +x /usr/local/sbin/cpu-power-limit
sudo systemctl enable --now cpu-power-limit.service
```

### Raw MSR notes

`MSR 0x610` is `PKG_POWER_LIMIT`: PL1 power in bits 14:0 (1/8 W units), PL2 power in
bits 46:32, the two time windows in bits 23:16 and 55:48, and the lock in bit 63.
`MSR 0x606` (`RAPL_POWER_UNIT`) gives the units. Prefer `powercap`/a tool that packs
the fields over hand-editing a 64-bit `wrmsr`; a wrong value can trip the lock or
leave the package at a very low limit until reset.

## 6. What the board can actually take

The limits are not the only constraint:

* **65 W design.** The datasheet lists CPU TDP options of **35 W and 65 W**, and
  Advantech's coolers are 45 W (80 × 80 × 32 mm) and 65 W (92.9 × 92.9 × 46 mm).
  PL1 above 65 W is outside the board spec even though the 12–24 V input and the
  4-pin ATX12V can deliver more current.
* **VRM has no telemetry.** There is no VRM temperature sensor in the BIOS/EC, so a
  sustained over-limit shows up as instability or throttling, not a reading.
* **Non-K CPUs.** Intel's help text in this very BIOS says PL1 must be between the
  minimum power limit and **TDP**; the BIOS clamps it. The MSR is unlocked, so a
  runtime tool can try more, but the CPU fuse table may refuse, and the VRM is the
  real ceiling. **PL2 is the useful knob** on a locked chip.
* **95 W K-SKUs** (i7-8700K, i5-8600K) exceed the 65 W cooler/VRM rating even at
  stock TDP. They may post thanks to the Coffee Lake microcode in this image, but treat
  them as burst-only and expect throttling.
* **12–24 V input**: a 4-drive NVMe lab adds 15–25 W. A 120–180 W brick has headroom
  for a 65 W CPU plus drives; a 60–84 W adapter does not.

## 7. Suggested profiles

| Goal | PL1 | PL2 | Window | Notes |
|---|---|---|---|---|
| Quiet / fanless-ish | 35 W | 45 W | 28 s | T-series-like behaviour, 80 mm cooler |
| Balanced NAS | 45 W | 65 W | 28 s | good for 4× NVMe and a 120–150 W brick |
| Stock | TDP (65 W) | 1.25 × TDP (~81 W) | 1 s / default | what the BIOS does today |
| Burst lab | 65 W | 90 W | 8–28 s | only with the 92 mm cooler and case airflow |
| Not recommended | > 65 W | > 100 W | — | outside VRM/cooler design |

## 8. Verify and measure

```bash
sudo turbostat --quiet --show PkgWatt,PkgTmp,Busy%,Bzy_MHz --interval 1
# or
sudo apt install linux-cpupower && sudo cpupower frequency-info
sensors            # NCT6776: CPU/system temperatures, fan RPM
```

Check that the reported package power respects the new PL1 after ~30 s of load
(`stress-ng --cpu 0 --timeout 60s`), and that `PkgTmp` stays below ~80 °C with the
65 W cooler. If the package sits at 800 MHz, the VRM/thermal throttle is active.

## 9. FAQ

**Does this need a BIOS mod?**
No. The questions exist; at most you unhide the pages with `setup_var 0x1013 0x1`.
The watt values can be set at runtime through RAPL, which is safer than reflashing.

**Why is my PL1 field grayed out?**
Either SpeedStep is off (`0x74 == 0`), Config TDP is active (`0xCE4 == 1`), the CPU
capability flag `0xC3F` grays the page, or the CPU is a non-K SKU and the value is
outside the allowed min..TDP range.

**Will the settings survive a BIOS update or CMOS clear?**
No. A CMOS clear / "Load Setup Defaults" resets the Setup variable to PL1 = TDP,
PL2 = 1.25 × TDP, and the MSR lock to Disabled. Re-apply the systemd unit after
updates.

**Can I raise PL1 above the CPU TDP?**
On a locked SKU the BIOS will refuse and clamp. Runtime MSR writes may be accepted,
but the board is a 65 W design; this is an experiment, not a configuration.

**Does the ME/EC enforce a limit?**
No separate EC power limit is exposed in this image; the package RAPL limit is the
controlling one. The ME can affect turbo residency indirectly, but not PL1/PL2.

## 10. References

* Intel 6th/8th Gen Core datasheets, §6.4 (CFG straps) and the RAPL MSR chapter.
* Linux `powercap`/`intel_rapl` documentation: `Documentation/power/powercap/powercap.rst`.
* Intel SDM Vol. 4, `MSR_PKG_POWER_LIMIT` (0x610) and `MSR_RAPL_POWER_UNIT` (0x606).
* AIMB-275 datasheet: CPU TDP 35/65 W, cooler options 45/65 W, 12–24 V DC input.

---

*Part of the [AIMB-275 dossier](README.md). Firmware evidence: `stock1.bin` Setup
IFR + Setup default variable; see README §4.7.1 for the summary.*
