# Luckfox Lyra SDK — Distilled Snapshot

## Source

Google Drive folder: `https://drive.google.com/drive/folders/1l2ixhfw53J3eZunyvHMnw2DYViH9d8Cx`
File: `Luckfox_Lyra_SDK_250815.tar.gz` (latest version, dated 2025-08-15)
Contains a `repo` mirror (Google Repo Tool) for the Rockchip RK3506, manifest `rk3506_linux6.1_release.xml`.

## Distillation (history stripped, current state only)

```bash
# Prerequisite: Python 2 (required by the repo launcher), e.g. via conda-forge:
micromamba create -y -n py27 -c conda-forge python=2.7

# Check out the working tree from the local mirror (no network, no git history):
python2 .repo/repo/repo sync -l -j4
```

The command above reproduces this snapshot from the **original download's** `.repo` mirror;
it is not run against a clone of this repository (the mirror is not part of the repo, see below).

## Not included in this repository

This repository is the distilled SDK **source** snapshot. Two items of the SDK were
deliberately not uploaded:

1. **`prebuilts/gcc/`** — the vendored ARM GCC cross-toolchains (~1.3 GB:
   `gcc-arm-10.3-2021.07-x86_64-arm-none-linux-gnueabihf` and
   `gcc-arm-none-eabi-10-2020-q4-major-x86_64-linux`).
   Why: (a) prebuilt binaries fetched by the build system, not SDK source;
   (b) ~1.3 GB far exceeds a sensible repository size;
   (c) licensed GPL-3.0 + GCC Runtime Library Exception, so publicly redistributing the
   binaries triggers a corresponding-source obligation (GPLv3 §6) that is cleaner to avoid
   by referencing the upstream.
   Restore: via the original `repo sync`, or download the matching Arm GNU toolchains from Arm.

2. **`tools/linux/PinDebug/pin_debug_tool_v1.11_for_linux.tar`** (171 MB) — a prebuilt
   pin-debug tool archive.
   Why: it exceeds GitHub's hard 100 MB per-file limit, so the push is rejected outright
   (Git LFS would be required). It is a prebuilt tool, not source.
   Restore: extract it from the original `Luckfox_Lyra_SDK_250815.tar.gz` (path above).

The `repo` mirror `.repo/` (bare Git objects / full history) is also not uploaded, by design:
this repository is the flattened, history-free source, not the mirror.

## Licenses

Verified against the actual LICENSE / COPYING / NOTICE files and the SPDX identifiers
in the source headers of each component.

| Component | Path | License |
|---|---|---|
| Linux kernel | `kernel-6.1/` (`kernel/` = symlink) | GPL-2.0-only (with syscall-note) |
| U-Boot | `u-boot/` | GPL-2.0-or-later (+ BSD/MIT for individual files, see `Licenses/`) |
| Buildroot | `buildroot/` | GPL-2.0-or-later (infrastructure; packages individually licensed) |
| RT-Thread (RTOS) | `rtos/` | Apache-2.0 (majority) + BSD-3-Clause; `rtthread-apps`: LGPL |
| Yocto/Poky + meta-* | `yocto/` | MIT (metadata) + GPL-2.0-only (e.g. BitBake); poky docs incl. CC-BY-SA-2.0-UK |
| HAL (MCU) | `hal/` | BSD-3-Clause (majority) + Apache-2.0 |
| Loader/firmware blobs | `rkbin/` | Rockchip-proprietary, redistribution explicitly permitted |
| ARM toolchains | `prebuilts/gcc/` | GPL-3.0 + GCC Runtime Library Exception — **not uploaded, see "Not included" above** |
| common_algorithm | `external/common_algorithm/` | BSD-3-Clause |
| linux-rga | `external/linux-rga/` | Apache-2.0 |
| recovery | `external/recovery/` | Apache-2.0 |
| alsa-config | `external/alsa-config/` | Apache-2.0 (AOSP, via NOTICE) |
| rk_pcba_test, rkscript, rktoolkit, rkupdate, rockchip-test | `external/…` | BSD-3-Clause |
| rkwifibt | `external/rkwifibt/` | BSD-3-Clause wrapper — bundled WiFi/BT firmware is third-party [1] |
| rkwifibt-app | `external/rkwifibt-app/` | no LICENSE in tree |
| rockit (media runtime) | `external/rockit/` | no LICENSE in tree — proprietary (binary `.so`/`.a` only) |
| rk_ethercat_release | `external/rk_ethercat_release/` | no LICENSE in tree — kernel modules without source [2] |
| security/bin (OP-TEE) | `external/security/bin/` | Apache-2.0 (via NOTICE) |
| security/rk_tee_user | `external/security/rk_tee_user/` | BSD-2-Clause (majority) + Apache-2.0; some GPL-2.0 (test/host) |
| security/librkcrypto | `external/security/librkcrypto/` | no explicit LICENSE — Rockchip copyright; some third-party files Apache-2.0 [3] |
| Flash/pack tools | `tools/` | no LICENSE in tree — Rockchip-proprietary (one 171 MB file omitted, see above) |
| device configs | `device/rockchip/` | no root LICENSE — source headers predominantly GPL-2.0 |
| lvgl_demo | `app/lvgl_demo/` | no LICENSE in tree (LVGL upstream is MIT, but no text shipped) |
| rkadk | `app/rkadk/` | BSD-3-Clause |
| Documentation | `docs/` | Rockchip "AS-IS" disclaimer, no explicit redistribution grant [4] |

### Notes

- **"no LICENSE in tree"** = no license text found in that component; the default is
  all-rights-reserved. Clarify separately before any public redistribution.
- **[1] rkwifibt** — the Rockchip glue code is BSD-3-Clause, but the shipped WiFi/BT
  firmware blobs originate from third parties (e.g. Broadcom/Cypress/Realtek/AIC) and
  carry their own, often restrictive, redistribution terms. Verify per blob.
- **[2] rk_ethercat_release** — ships only prebuilt kernel modules (`.ko`) and
  `libethercat.so` without source. The code derives from the IgH EtherCAT Master, whose
  upstream license is GPL-2.0 (kernel modules) / LGPL-2.1 (userspace library);
  redistributing the binaries without the corresponding source is a GPL concern.
- **[3] librkcrypto** — no top-level license file; the main sources carry only a bare
  Rockchip copyright with no explicit grant, while a small number of bundled third-party
  files declare Apache-2.0. Do not treat the whole component as Apache-2.0.
- **[4] docs** — the doc license is the Rockchip "AS-IS" disclaimer (no grant); embedded
  kernel-doc patches additionally carry GPL-2.0 headers.
