# Advantech AIMB-275 — research dossier

**What it is:** an industrial Mini-ITX (170 × 170 mm) motherboard for Intel LGA1151
Skylake/Kaby Lake (6th/7th gen Core i7/i5/i3/Pentium/Celeron), with Intel Q170 or
H110 PCH, **12–24 V wide-range DC input**, dual GbE, PCIe x16, M.2 B-key and a
full-size mini-PCIe/mSATA slot. Launched mid-2016; Rev A2 (2019/2020) added official
7th-gen support and dropped VGA.

| | |
|---|---|
| ![AIMB-275G2 top 3/4](img/aimb275-g2-top-3q.png) | ![AIMB-275 official product photo](img/aimb275-datasheet-photo.png) |
| ![A2 rear I/O](img/aimb275-a2-rear-io.png) | ![A1 rear I/O](img/aimb275-a1-rear-io.png) |

*(All 12 images, clickable: [`gallery.html`](gallery.html))*

---

## 1. Which "stripped down" version do you have?

Advantech sold AIMB-275 in three retail SKUs plus OEM/custom builds. Check the
silkscreen near the edge (e.g. `AIMB-275G2-00A5E`, `AIMB-275VG-00A5E`,
`AIMB-275L-00A5E`) and count the rear ports.

| SKU (A2, 2019+) | PCH | DP | VGA | LVDS/eDP | HDMI | GbE | COM | M.2 | SATA | USB3 / USB2 | mini-PCIe | PCIe x16 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **AIMB-275G2-00A5E** (full) | Q170 | 1 | – | 1 / (1) | 1 (HDMI 2.0) | **2** | 2 | **1** | 3 (1 SATA-DOM) | 6 / 4 | 1 (F/S) | 1 |
| **AIMB-275VG-00A5E** | Q170 | 1 | – | – | 1 | **1** | 2 | – | 3 | 4 / 4 | 1 (F/S) | 1 |
| **AIMB-275L-00A5E** | **H110** | 1 | – | – | 1 | **1** | 2 | – | 3 | 4 / 4 | 1 (F/S, **mSATA only**) | 1 |

Rev A1 (2016) SKUs (`…-00A1E`) are the same but **have VGA** and triple-display
includes VGA. Photos of both rear-I/O layouts are in `img/`.

Also seen in the wild: OEM "reduced" boards (one LAN, no HDMI/DP, no LVDS) used in
panel PCs / KUKA IPC cabinets, and boards with BOM options removed (TPM, audio amp,
eDP). Those are not catalog SKUs — identify by silkscreen and by which connectors are
physically populated. **The G2 vs VG/L distinction matters most for mods: only G2 has
M.2 and LVDS/eDP, and only Q170 SKUs have the second LAN + vPro/AMT.**

---

## 2. Full specifications

| Item | Detail |
|---|---|
| Form factor | Mini-ITX, 170 × 170 mm, ~365 g |
| CPU | LGA1151 (v1), Intel 6th/7th gen Core i7/i5/i3/Pentium/Celeron, up to 65 W TDP validated |
| PCH | Intel Q170 (G2/VG) or H110 (L); DMI x4 |
| BIOS | AMI UEFI, **128 Mbit (16 MB) SPI flash**; socketed flash + SPI programming header |
| Memory | 2 × 260-pin DDR4 SO-DIMM, non-ECC, dual channel; 2133 MHz (Skylake) / 2400 MHz (Kaby); **32 GB max** (16 GB/module), 64 GB physically possible |
| Graphics | Intel HD Graphics; DP 1.2 (3840×2160@60), HDMI 2.0 (4096×2160@60), LVDS dual-channel 48-bit (1920×1200), eDP (4096×2160@60, BOM option); triple display |
| Expansion | 1 × PCIe x16 Gen3 (CPU), 1 × full-size mini-PCIe (mSATA co-lay), 1 × M.2 B-key 2242/2280 (SATA + PCIe x2 + USB2 for 3G) |
| Storage | 3 × SATA III (2 normal + 1 SATA-DOM with power header); Intel RST RAID 0/1/5/10 |
| LAN | LAN1 Intel **i219LM** (vPro/AMT 11.0), LAN2 Intel **i211AT** (datasheet says i210AT — the manual and board say i211AT) |
| USB | 6 × USB 3.0 (4 rear + 2 header), 4 × USB 2.0 (headers) |
| Serial | COM1 RS-232 (5 V/12 V on pin 9 via jumper), COM2 RS-232/422/485 (BIOS-selectable) |
| Audio | Realtek ALC892, 3 rear jacks, front-panel header, SPDIF header, optional amp header |
| Industrial | 8-bit GPIO (NCT6776D), watchdog 1–255 s/min → system reset, case-open detect, S5 RTC wake, PECI 3.0, TPM 1.2/2.0 (optional Infineon) |
| Power | **12–24 V DC** via 4-pin lockable Phoenix (2×Vcc, 2×GND); idle ~19.3 W (i7-6700, 2×8 GB); ATX 12 V + 5 VSB headers also present |
| Environment | 0–60 °C operating, −40–85 °C storage, 5–95 % RH non-condensing, ESD level 4 |
| Software | WISE-PaaS/DeviceOn, SUSI/embedded APIs, Win 7/8/10, Linux |

### Power / thermal reality check
* 65 W CPU + board + 2 DIMMs + drives ≈ 80–110 W at the wall; a **120 W 12 V brick is
  the minimum**, 150–180 W is comfortable. 19 V is the common choice (same as many
  mini-ITX bricks); 12 V avoids a buck stage for the 12 V rail.
* The CPU VRM is a small 3–4 phase design. **T-series (35 W) parts are the safe
  choice for fanless**, 65 W non-K is fine with airflow, 7700K (91 W) is not validated.

---

## 3. Board map, jumpers, pinouts

Full labelled diagrams: `img/aimb275-top-layout-and-jumpers.png`,
`img/aimb275-bottom-layout.png`, `img/aimb275-block-diagram.png`,
`img/aimb275-board-layout-clean.png`. All images: open `gallery.html`.

### Jumpers
| Label | Function | Settings |
|---|---|---|
| JCMOS1 | Clear CMOS | 1-2 keep (default), 2-3 clear |
| JFP1 | PWR switch, HDD LED, SMBus, speaker, reset | 2×6 header |
| JFP2 | Power LED + keyboard lock | 1-2 LED, 4 lock |
| JWDT1/JOBS1 | Watchdog output + OBS beep | 2-3 WDT on (default), 1-2 disable; 4-5 beep |
| PSON2 | AT/ATX mode | 2-3 ATX (default), 1-2 AT |
| JLVDS1 | LVDS panel voltage | 2-4 = 5 V, 4-6 = 3.3 V (default), 3-4 = 12 V |
| JSETCOM2_V1 | COM2 pin 9 | RI# (default), 5 V, 12 V |
| JLVDS_VCON1 | LVDS VESA/JEIDA select | 3-pin |
| JSATAPWR1 | SATA-DOM power | GND (default) / +5 V |
| JCASEOP_SW1 | Case-open switch type | normal-close / normal-open |

### Pinouts worth having
**DCIN1 (4-pin Phoenix):** 1 GND, 2 Vcc 12–24 V, 3 Vcc 12–24 V, 4 GND

**GPIO1 (2×5):** 1 GPIO0 … 7 GPIO3, 2 GPIO4 … 8 GPIO7, 9 +5 VSB, 10 GND

**SPI_CN1 (2×4, for external programmer):** 1 CS#, 2 +3.3 V, 3 MISO, 4 NC,
5 NC, 6 SCK, 7 GND, 8 MOSI

**LPC1 (2×7):** 1 CLK33M, 2 AD1, 3 RESET#, 4 AD0, 5 FRAME#, 6 +3.3 V, 7 AD3,
8 GND, 9 AD2, 10 SMB_CLK, 11 SERIRQ, 12 SMB_DATA, 13 +5 VSB, 14 +5 V

**ATX12V1 (2×2):** 1–2 GND, 3–4 +12 V
**ATX_5VSB1 (3-pin):** 1 +5 VSB, 2 GND, 3 PS_ON#

**Fan headers:** standard 4-pin (12 V, GND, tach, PWM). CPU fan + 2 system fans.

**M.2 B-key:** SATA, PCIe x2, USB 2.0, 3.3 V — 2242/2280 mounting holes on the
bottom side (M.2_1/2/3).

---

## 4. Mods — from easy/safe to experimental

### 4.1 CPU (biggest performance lever)
* **Drop-in:** i7-7700 / i5-7500 (65 W), or i7-7700T / i5-7500T (35 W) for fanless.
  Rev A2 BIOS officially supports 7th gen. 6th gen works too.

The datasheet's validated list (KBL-S = Kaby Lake, SKL-S = Skylake):

| CPU | Cores | Base (KBL / SKL) | L3 | TDP |
|---|---|---|---|---|
| i7-7700 / i7-6700 | 4 | 3.6 / 3.4 GHz | 8 MB | 65 W |
| i7-7700T / i7-6700TE | 4 | 2.9 / 2.4 GHz | 8 MB | 35 W |
| i5-7500 / i5-6500 | 4 | 3.4 / 3.2 GHz | 6 MB | 65 W |
| i5-7500T / i5-6500TE | 4 | 2.7 / 2.3 GHz | 6 MB | 35 W |
| i3-7101E / i3-6100 | 2 | 3.9 / 3.7 GHz | 3 MB | 54 W / 51 W |
| i3-7101TE / i3-6100TE | 2 | 3.4 / 2.7 GHz | 3 MB | 35 W |
| Pentium G4400 / G4400TE | 2 | 3.3 / 2.9 GHz | 3 MB | 54 W / 35 W |
| Pentium G3900 / G3900TE | 2 | 2.8 / 2.6 GHz | 2 MB | 51 W / 35 W |

The T-suffix parts are the same silicon at 35 W — they are what makes a fanless build
realistic, and they cost very little used.
* **8th-gen Coffee Lake (community-verified):** owners report **i5-8400 and i5-8500
  running on AIMB-275 Q170** using **CoffeeTime** BIOS modding plus the standard
  LGA1151 pin isolation/bridge ("pinmod"). Requirements: 8th-gen microcode
  (CPUID 906EA/906EB), ME 11.8 firmware, updated GOP/VBIOS; then the CPU-side
  pinmod (insulate the reserved pads, bridge the two configuration pads — use the
  community pinmod map, it varies by board/CPU).
  * Expect **iGPU output to be the fragile part** after the mod; a discrete GPU in
    the x16 slot is the reliable display path.
  * **9th gen (9700/9900) is not recommended**: 8-core parts need extra pin work and
    frequently fail to POST on 100-series; the VRM is also marginal.
* **Xeon E3 v5/v6:** not supported (needs C232/C236).
* **Overclocking:** none. Q170 has no CPU/BCLK OC; the BIOS "Overclocking lock" is an
  ME/fuse lock, not a feature.

### 4.2 Memory
* 2 × 32 GB DDR4-2666 SO-DIMM = **64 GB** is the interesting experiment (Q170 and
  Kaby Lake IMCs support 64 GB; the 32 GB figure is the validated config). Use
  2Rx8 modules and expect them to train at 2400/2133.
* 48 GB (32 + 16) has been reported working in the wild; mixed sizes run in flex mode.

### 4.3 Storage
* **M.2 slot is B-key**: M-key NVMe drives physically will not fit. Use **B+M-key**
  SATA or NVMe drives (NVMe runs at PCIe 3.0 x2, ~1.6–1.7 GB/s). The BIOS has an
  **NVMe Configuration** page, so NVMe boot is plausible — test before relying on it.
* **PCIe x16 → NVMe adapter** gives a full x4 link and is the clean way to boot NVMe;
  leaves no room for a GPU.
* **mini-PCIe is mSATA-capable** — good for a small boot/cache SSD, leaving SATA free.
* **SATA-DOM** on SATA3 with the JSATAPWR1 jumper is the industrial boot option.
* RAID 0/1/5/10 works via Intel RST (Windows) or mdadm (Linux).

### 4.4 PCIe bifurcation (experimental, community-reported)
Q170 does not officially support x16 bifurcation and the BIOS exposes no option.
Owners have enabled **x8/x4/x4** on this board with a modded BIOS and a bifurcation
riser, reporting "partial success". Q170 is in the chipset group that *can* do it
(C/Q/W/Z can; B/H cannot) and LGA1151 tops out at **x8/x4/x4** — x4x4x4x4 does not
exist on this platform. Treat as a lab experiment: needs a BIOS backup, a spare flash
chip, and a riser that supports the mode.

**→ Full procedure, including the two no-flash options, is in §4.5 below.**

### 4.5 How to add the bifurcation option to the BIOS (step by step)

There are three routes, in increasing order of risk:

| Route | What you touch | Reversible? | Chance it works on this board |
|---|---|---|---|
| **A. Runtime variable flip** | nothing — a setup value in NVRAM | yes, clear CMOS | low–medium (only if the question exists but is hidden) |
| **B. Unlock + reflash BIOS** | the 16 MB image | yes (keep stock chip/dump) | low–medium, same precondition as A |
| **C. CPU CFG strap bridge** | the CFG strap pads on the bottom of the CPU | yes, peel the tape off | high — this is what the AIMB-275 community actually did |

Work in the order **A → C**, and keep B for when you want the setting to survive a
CMOS clear. B is the only route that persists, and the only one that can leave you with
a board that does not POST — so it goes last, with a verified dump and a spare chip on
the bench.

#### 4.5.0 Know what the stock BIOS gives you

From the manual, `Chipset → System Agent (SA) Configuration` exposes: `VT-d`,
`Above 4GB MMIO BIOS assigment [Disabled]`, `Graphics Configuration`, `LCD Control`,
`DMI/OPI Configuration`, and `PEG Port Configuration`. The PEG page has exactly four
items — `Enable Root Port`, `Max Link Speed`, `Program PCIe ASPM after OpROM`,
`Program Static Phase1 Eq` — plus Gen3 preset/hint tables. **No bifurcation item, and
only one PEG port** (Intel calls it *IIO PCIe Port 1*; the other IIO ports are not wired
on a single-slot board).

Bifurcation is an Intel IIO setup question that AMI's reference code includes on
server/workstation builds and normally *omits* from client builds. So step zero is
always an inventory: does the question exist in your image at all? If it does not
exist, no amount of menu-unlocking will produce it, and you go to Route C.

#### 4.5.1 Back up the flash first (non-negotiable)

`SPI1` is a socketed SOP-8 16 MB chip and `SPI_CN1` is a programming header, so a
CH341A costs ~€8 and gives you a full recovery path.

```bash
sudo flashrom -p ch341a_spi -r stock1.bin
sudo flashrom -p ch341a_spi -r stock2.bin
sha256sum stock1.bin stock2.bin     # hashes must match
```

* Program the chip **out of the socket**, not with a clip on a powered board.
* Many black CH341A clones drive the data lines at 5 V; the flash is 3.3 V. Use a
  3.3 V-modified/"green" programmer. A 5 V programmer will eventually kill the chip.
* Keep `stock1.bin` on another machine, and buy a spare **W25Q128** (3.3 V, SOP-8) so
  you always have a known-good chip to drop in.
* Confirm the dump is exactly 16 MiB and that UEFITool parses it without errors before
  you trust it.

Also download the official Advantech BIOS for AIMB-275 from their support site. If you
are going to flash a modified image, **mod the official image, not your dump** — you
avoid inheriting whatever state the previous owner left behind. But check its size: if
it is a capsule or a BIOS-region-only file rather than a full 16 MB image, flash only
the BIOS region (`flashrom -l layout.txt --image=bios -w ...` or `fpt -bios -f`), or
strip the capsule with UEFITool first.

#### 4.5.2 Route A — flip the hidden variable (no flash, ~10 minutes)

This only works if the question is present in the image but *suppressed* (invisible)
in the form. You change the value directly, bypassing the hidden form.

1. **Inventory.** Open the image in **UEFITool**, find the Setup module, extract it,
   then run an IFR extractor on it:

   ```bash
   UEFIExtract stock1.bin             # or just open it in UEFITool (GUI)
   ifrextract Setup.bin setup.txt     # IFR binary -> readable text
   grep -n -i "bifurc\|IIO PCIe\|PEG" setup.txt
   ```

   In the IFR, a question looks like `One Of: Bifurcation, VarStoreInfo (VarOffset/VarName): 0x1A3, ...`
   followed by `One Of Option: x8x4x4, Value (8 bit): 0x02` lines. **Write down the
   offset, the width (8/16/32-bit) and the exact option value** — the values are not
   standardised; read them, do not guess.

2. **Boot a UEFI shell** from USB (the board boots `.efi` fine; enable UEFI boot, CSM
   off is not required) and either:
   * `RU.EFI` → `UEFI Variables` → `Setup` → edit the byte at that offset, then
     `Ctrl+W` to write; or
   * GRUB with `grub-mod-setup_var` and `setup_var_cv <VarStoreGUID> <offset> <value>`.

3. **Full power-off, then boot.** Do not "Load Setup Defaults" — that rewrites the
   variable and undoes your edit. Check `lspci -tv`.

If the value silently reverts after a cold boot, the varstore is checksummed/validated
by the platform and this route is closed — go to Route C (or B).

#### 4.5.3 Route B — unlock the menu and reflash (persistent)

1. Open the image in **AMIBCP 5.x** (must be an Aptio V build; 5.02.0031+ handles most
   AIMB-275-era images). Navigate `Setup Configuration → System Agent (SA)
   Configuration → PEG Port Configuration` (and `IIO Configuration` if it is listed).
2. Find `Bifurcation` / `PEG Port Bifurcation`. In the **Access** column change it from
   `Default` (or `Suppress` / `Gray Out`) to **`USER`**. Do the same for the parent
   page if the page itself is hidden.
3. While you are in there, also set `Above 4GB MMIO BIOS assigment` to **Enabled**
   (multi-device enumeration needs it), turn ASPM off, and leave `Max Link Speed` on
   `Auto`/Gen3.
4. Save as a new image and check it in UEFITool: no "invalid" volumes, no parse errors.
5. **Flash.** Either externally with the CH341A (`flashrom -p ch341a_spi -w modded.bin`,
   then `-v` to verify and compare SHA-256), or in a UEFI shell with `AFUEFIx64 /GAN`
   (the `/GAN` switch bypasses the signature check). External flashing is safer here
   because `SPI1` is socketed.
6. **Clear CMOS** (`JCMOS1`), boot into setup, set `Bifurcation` to **x8x4x4**,
   `Above 4GB` = Enabled, save and exit.
7. If it does not POST: power off, swap in the stock chip, done. This is exactly why
   you bought the spare.

If AMIBCP refuses the image (common with later Aptio V builds), use **UEFI-Editor**
(BoringBoredom) instead: extract the IFR, load it in the tool, set the item's access
level to USER, then load the BIOS image and save the patched copy. Hand-patching the
`Suppress If` opcodes in a hex editor is possible but fragile and build-specific — only
worth it if both tools fail.

#### 4.5.4 Verify it actually bifurcated

```bash
lspci -tv                                   # root complex tree
sudo lspci -s 00:01.0 -vv | grep -E "LnkCap|LnkSta"
```

A working x8/x4/x4 looks like: root port `00:01.0` training **x8**, with three
downstream buses (typically `01`, `02`, `03`) carrying `01:00.0`, `02:00.0`, `03:00.0`.
`LnkSta: Speed 8GT/s, Width x8` / `x4` / `x4` is the confirmation. `nvme list` should
show all drives, each at Gen3 x4 (~3.5 GB/s in a quick `fio`/`dd` test).

#### 4.5.5 Route C — no BIOS mod at all: bridge the CFG pads on the CPU

This is the WatchMySys technique (linked in §6) and the one AIMB-275 owners report
using: Intel exposes **CFG strap pins** on the LGA1151 package that tell the CPU how to
split its 16 PEG lanes at reset. Workstation boards pull them to ground to offer
x8/x8 or x8/x4/x4; you can do the same by **bridging the CFG pads on the underside of
the CPU to an adjacent ground pad** with copper tape or folded aluminium foil.

* Q170 is in the supported group. B/H-series chipsets split the lanes but **do not
  enumerate the extra devices** — so this would not work on the AIMB-275L (H110),
  which matters if you compare boards.
* Max is **x8x4x4** on this generation. No x4x4x4x4, ever.
* Fully reversible (remove the tape). Works with Skylake through Coffee Lake client
  parts; pre-Skylake Xeons are limited to x8x8.
* It also explains the "partial success" reports: if the strap takes but the device
  tree still shows one link, you are in the B/H failure mode, or the riser is wired for
  a different split.

#### 4.5.6 Triage: it still does not work

| Symptom | Most likely cause |
|---|---|
| One link at x16, no extra devices | BIOS/strap never applied the split |
| One link at **x8**, extra devices missing | split applied, chipset refuses to enumerate (B/H) or Above 4GB is disabled |
| Three links train, NVMe not in OS | Above 4GB off, or the adapter's power (M.2 cards need 3.3 V from the riser) |
| Extra devices appear/disappear | riser signal integrity — shorten it, drop to Gen2 to test, check the 12 V/3.3 V rail |
| Option vanished after reboot | Route A only; the varstore was reset. Use Route B or C |

#### 4.5.7 What to plug into a bifurcated VG

VG has no M.2 slot, so the realistic four-drive layout is:

* **PCIe x16 → x8/x4/x4 passive riser** (~€20–25) — the split must be done by the
  CPU/BIOS, a passive riser cannot do it itself.
* x8 slot: one NVMe on an x4→x8 adapter (it trains x4 and burns 4 idle lanes — that is
  normal; an x8 dual-M.2 card will **not** work, because x4x4 inside the x8 is the
  unsupported x4x4x4x4 pattern).
* Two x4 slots: M.2 M-key NVMe adapters.
* **4th/boot drive:** mini-PCIe → NVMe x1 adapter (~€15), capped at ~1 GB/s Gen3 x1,
  which is fine for a boot/OS drive.
* Power: 4 NVMe ≈ 15–25 W peak; a 120–180 W 12 V brick is plenty, but check the riser's
  3.3 V delivery — cheap risers feed 3.3 V from the slot and can brown out under load.

If you would rather not touch the BIOS at all, a **PEX-switch-based** card (PLX/PEX8724)
splits x16 into 4×x4 with no CPU/BIOS support, at the cost of ~€80–150, extra latency,
and worse idle power — a legitimate plan B for a lab box.

### 4.6 BIOS modding / recovery (this board is unusually friendly)
* **SPI1 is a socketed 16 MB SPI flash** and there is a dedicated **SPI_CN1 header**
  (pinout above). That means: CH341A/clip or header cable, `flashrom -p ch341a_spi`,
  full 16 MB dump before touching anything — and **instant recovery** if a mod bricks.
* Buy spare W25Q128 (3.3 V, SOP-8) chips: swap between stock and modded BIOS, or wire
  a dual-BIOS switch. This is the single best safety mod for Coffee Lake experiments.
* Hidden BIOS menus can be unlocked with AMIBCP (bifurcation, Above 4G, ASPM, etc.).
* ME can be disabled in BIOS (Aerotech ships it disabled for real-time motion
  control) — useful for latency tuning, but disabling ME breaks vPro/AMT.
* BIOS updates are distributed by Advantech (support → AIMB-275 → BIOS). Flash with
  AFUEFI from DOS/Windows, or externally via SPI.

### 4.7 Power mods
* **12 V battery/UPS direct**: the 12–24 V input accepts a 12 V SLA or 4S LiFePO4
  pack without a converter — a natural fit for a DIY NAS/edge box.
* **ATX PSU**: feed 12 V into ATX12V1 and 5 VSB + PS_ON# into ATX_5VSB1 (never
  connect DCIN at the same time). This lets you use a standard ATX supply and its
  5 VSB for soft power.
* **GPU**: with a 12 V input the PCIe slot's 75 W is limited by your brick/DC-DC.
  A 75 W slot-powered card (GTX 1650 class) is the realistic ceiling; cards needing
  aux power need a separate 12 V feed. Intel Arc needs ReBAR/Above-4G and is a bad
  fit for Q170.
* Add an inline fuse and reverse-polarity protection; the Phoenix connector is
  lockable but the board has no input protection beyond the buck stage.

### 4.8 Cooling / chassis
* Standard **LGA115x cooler mounting (75 mm)**, and the board has a factory
  backplate. Advantech lists 45 W (80×80×32 mm) and 65 W (92.9×92.9×46 mm) coolers.
* Chassis: AIMB-B2000-15ZE (150 W ATX) or B2000-00YE (external 60/84 W adapter).
* Risers: AIMB-RF10F-01A1E (1U, 1× x16) and AIMB-RF20F-02A1E (2U, x16 → 2× x8).

### 4.9 I/O and industrial features as mods
* **mini-PCIe**: 2.5 GbE (i225/i226), Wi-Fi 6, USB 3.0, CAN, extra serial, FPGA.
  It is PCIe x1 + USB 2.0 — no SATA unless you use an mSATA module.
* **GPIO1** (8-bit) + **watchdog** header make this a good relay/valve/controller
  board; **S5 RTC wake** gives scheduled power-on, **case-open** input for alarms.
* **LPC1** can host a POST card; a TPM 2.0 module on LPC *may* work but only if the
  BIOS has the matching Infineon support — the catalog TPM is a soldered BOM option.
* **COM1 5 V/12 V on pin 9** powers barcode scanners/sensors directly; COM2 does
  RS-485 for long industrial runs.
* **G2 only:** LVDS/eDP header + inverter header turns the board into a panel-PC
  driver (JLVDS1 sets 3.3/5/12 V, JLVDS_VCON1 sets VESA/JEIDA).

### 4.10 Software
* Linux: everything mainstream (i219LM, i211, ALC892, NCT6776 sensors via
  `nct6775`). Fan control through the Super I/O; watchdog through the Advantech
  SUSI API or the Super I/O watchdog driver.
* Windows 11: needs TPM 2.0 + Secure Boot + an unsupported-CPU bypass. Win 10 LTSC
  is the pragmatic OS; 7/8 need XHCI drivers (use PS/2 to install).
* vPro/AMT 11 works only with a vPro-capable CPU + Q170 + i219LM.

---

## 5. Known quirks and gotchas

1. **A1 vs A2:** Rev A1 has VGA; Rev A2 removed it. Datasheets differ. Don't trust a
   single PDF for port layout — check your board.
2. **LAN2 controller naming:** datasheet says i210AT, manual says i211AT; the board
   photo shows i211AT. Driver packages may list both.
3. **Only G2 has M.2 + LVDS/eDP.** If yours is VG/L, storage expansion is
   mini-PCIe/mSATA + SATA only.
4. **H110 (L) has no RAID** and no vPro; it is the true "stripped" board.
5. **M.2 is B-key**, so most consumer NVMe drives don't fit.
6. **BIOS chip is 16 MB** — keep a full dump; a corrupted ME region is not
   recoverable from the OS.
7. The 12–24 V input is **not hot-pluggable** and has no reverse-polarity
   protection; a miswired Phoenix connector is fatal.
8. After a Coffee Lake mod, **iGPU and sleep states are the usual casualties**;
   keep a stock flash chip to fall back to.

---

## 6. Community evidence (external links)

* r/homelab — *Gray sandwich mini NAS*: AIMB-275 Q170, CoffeeTime mod, bifurcation
  8x4x4, **i5-8500**, 32+16 GB DDR4-2666, 19 V 180 W PSU, ~18 W idle.
  <https://www.reddit.com/r/homelab/comments/1matcbo/gray_sandwitch_mini_nas/>
* r/pcmods — *CPU/Mobo mod for PCIe x16 bifurcation = partial success* (AIMB-275).
  <https://www.reddit.com/r/pcmods/comments/1h6y4wx/cpumobo_mod_for_pcie_x16_bifurcation_partial/>
* r/diyelectronics — LGA1151 socket mod thread; owner reports modded **8400/8500**
  on AIMB-275 Q170.
  <https://www.reddit.com/r/diyelectronics/comments/1g8m8ks/modifying_modern_cpu_sockets_lga1151_please_help/>
* r/homelab — bifurcation for an NVMe NAS on cheap AIMB-275 boards.
  <https://www.reddit.com/r/homelab/comments/1jv0lp0/how_to_run_bifurcation_my_nvme_nas/>
* r/homelabsales — listing for the "reduced version (only 1 LAN, no HDMI/DP, no LVDS)".
  <https://www.reddit.com/r/homelabsales/comments/1kxcjfg/fs_eupl/>
* Aerotech — AIMB-275 BIOS settings reference (CSM, ME disable, ASPM, Start after G3).
  <https://help.aerotech.com/automation1/user-guides/Automation1-PC-Based-Controller-Selection-Setup/Content/Online-Guide-Automation1-A3200-PC-Based-Controller-Selection-Setup/Advantech-AIMB-275-Specs-BIOS.htm>
* LTT — Coffee Lake/Refresh on Skylake/Kaby Lake guide (pinmod map + risks).
  <https://linustechtips.com/topic/1118475-guide-running-coffeelakerefresh-cpus-on-skykabylake-motherboards/>
* WatchMySys — *Forbidden PCIe bifurcation on Intel client CPUs* (the CPU CFG-pad mod,
  with a tested chipset/CPU matrix; Q170 + Skylake/Kaby = x8x4x4 OK, B/H = no).
  <https://watchmysys.com/blog/2026/07/forbidden-pcie-bifurcation-intel/>
* WinRAID — *How to bifurcate a PCIe slot* (BIOS-side walkthrough).
  <https://winraid.level1techs.com/t/guide-how-to-bifurcate-a-pci-e-slot/32279>
* WinRAID — CoffeeTime automated BIOS mod tool.
  <https://winraid.level1techs.com/t/tool-easy-automated-mod-tool-for-coffee-lake-bios/32795>
* UEFI-Editor — expose suppressed setup items without AMIBCP.
  <https://github.com/BoringBoredom/UEFI-Editor>
* `grub-mod-setup_var` — change a setup variable from GRUB, no flashing.
  <https://github.com/datasone/grub-mod-setup_var>
* TheRetroWeb — AIMB-275G2/VG Rev A2 page (photos, manuals, chip docs).
  <https://theretroweb.com/motherboards/s/advantech-aimb-275g2-vg-rev-a2>
* TheRetroWeb — AIMB-275L Rev A1.
  <https://theretroweb.com/motherboards/s/advantech-aimb-275l-rev-a1>

## 7. Repository contents

```
README.md                           this dossier
gallery.html                        click-through gallery (open it in a browser)

img/aimb275-g2-top-3q.png           G2/A2 board, 3/4 top view (hi-res)
img/aimb275-g2-front-3q.png         G2/A2 board, front 3/4 view
img/aimb275-datasheet-photo.png     official datasheet product photo
img/aimb275-a1-vga-top-3q.png       Rev A1 board with VGA + COM
img/aimb275-a2-rear-io.png          A2 rear I/O strip
img/aimb275-a1-rear-io.png          A1 rear I/O strip (VGA + COM)
img/aimb275-top-layout-and-jumpers.png   labelled top-side layout (Fig 1.1)
img/aimb275-bottom-layout.png       bottom side, M.2 mounting (Fig 1.2)
img/aimb275-block-diagram.png       board block diagram (Fig 1.3)
img/aimb275-board-layout-clean.png  clean labelled layout
img/aimb275-datasheet-p1.png        datasheet page 1 (specs table)
img/aimb275-datasheet-p2.png        datasheet page 2 (SKUs, accessories)
```

### Vendor documents (not redistributed here)

The datasheets and the 120-page user manual are Advantech copyright, so the PDFs are
not committed. Download them from the Advantech product page or the TheRetroWeb entries
in §6, drop them into `docs/`, and regenerate the greppable text:

```bash
pdftotext -layout docs/aimb275-manual.pdf            docs/manual.txt
pdftotext -layout docs/aimb275-datasheet.pdf         docs/datasheet.txt
pdftotext -layout docs/aimb275-datasheet-A1-2016.pdf docs/a1.txt
```

Board photos and layout figures in `img/` are cropped from those documents and are
included for identification and commentary; all trademarks remain Advantech's.

### Re-checking the claims in this README

Everything here is either quoted from those documents or linked to a public source, so
it should be reproducible: `grep -i "peg\|bifurc" docs/manual.txt` for the BIOS menu,
`pdftotext` for the datasheet SKU table, and the §6 links for the community reports.

---

## 8. Suggested build profiles

| Goal | Recipe |
|---|---|
| **Quiet NAS / home server** | i5-7500T or i7-7700T, 2×16 GB, SATA-DOM or mSATA boot, 3× SATA HDD, 19 V 150 W brick, stock BIOS |
| **Fast but sane** | i7-7700 (65 W), 2×16 GB DDR4-2400, B+M NVMe on M.2 (x2), 2× SATA, 92 mm cooler |
| **Max CPU on a budget** | i5-8500 + CoffeeTime BIOS + pinmod, **spare flash chip**, discrete GPU for display, 2×16 GB |
| **Panel PC / kiosk** | G2 only: LVDS or eDP panel, inverter on INV1, JLVDS1 set to panel voltage, 35 W CPU |
| **Industrial controller** | 35 W CPU, GPIO to relays, watchdog enabled, AT mode (PSON2), S5 RTC wake, COM2 RS-485 |
| **Multi-NVMe lab** | VG + CPU CFG-pad x8x4x4 mod (or modded BIOS) + x8x4x4 riser, 2× NVMe @x4 + 1× NVMe @x8-as-x4 + mini-PCIe x1 boot drive, i5-8500/i7-7700, 12 V 180 W |

---

### Your board: VG, multi-NVMe lab

Locked in from your answers: **AIMB-275VG-00A5E** (Q170, 1 LAN, no M.2, no LVDS) and the
target is experimenting with as many NVMe drives as the platform will take. Order of work:

1. **Dump the flash** and buy a spare W25Q128 (§4.5.1). ~€15 all-in, and it is the
   difference between "experiment" and "bricked".
2. **Try Route A** (§4.5.2) — costs one evening and zero risk. If the IFR inventory shows
   no bifurcation question (likely), skip straight to step 3.
3. **CFG-pad bridge on the CPU** (§4.5.5) — no BIOS edit, reversible, matches what other
   AIMB-275 owners report.
4. **Riser + 3 NVMe + mini-PCIe boot drive**, then verify with `lspci -tv` (§4.5.4, §4.5.7).
5. Only if 2–3 fail: **unlock and reflash the BIOS** (§4.5.3), with the stock chip on the
   bench next to you.

The BIOS will never give you x4x4x4x4 (CPU limit, not a menu limit), so 4 NVMe is the
practical ceiling: 3 behind the x16 slot, 1 on mini-PCIe.
