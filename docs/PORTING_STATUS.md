# Porting status

## Target
- Device: Samsung Galaxy J7 Prime SM-G610F (`on7xelte`).
- Intended release: Android 16 / LineageOS 23.
- Requested userspace: ARM64, retaining 32-bit compatibility where required by legacy vendor components.

## Completed source investigation
- Found a newer community source baseline in `Exynos7870-Revived-Beta`, branch family `lineage-19.1`.
- Verified the device tree, shared Exynos7870 common tree, proprietary vendor tree, and Exynos7870 kernel repository.
- Pinned their exact source commits in `manifests/lineage-19.1-reference.xml`.
- Confirmed ARM64 userspace plus a 32-bit secondary ABI in the common board configuration.
- Confirmed the device product inherits `core_64_bit.mk` and selects ARM64 GApps architecture.
- Confirmed the device tree selects `exynos7870-on7xelte_defconfig`, and that kernel defconfig exists.
- Updated `.github/workflows/device-source-audit.yml` to audit the four newer source repositories and upload the report.
- Added `docs/LINEAGE23_PORTING_PLAN.md` with the porting sequence and technical blockers.

## SELinux investigation and remediation
- Inspected the pinned kernel defconfig and found **two** enforcement bypasses: `CONFIG_CMDLINE="androidboot.selinux=permissive"` and `CONFIG_SECURITY_SELINUX_NEVER_ENFORCE=y`.
- Added `patches/kernel/0001-on7xelte-selinux-enforcing-baseline.patch` to remove both bypasses in the kernel configuration.
- Added `docs/SELINUX_ENFORCING.md` with application steps, policy checks, boot-argument audit, and device validation.
- The common device tree already includes the Exynos7870 SLSI SELinux policy makefile and the common vendor policy directory.
- **Not yet complete:** the final Android 16 policy has not been ported/compiled, and no enforcing boot or AVC validation has been performed. Do not call the ROM SELinux-enforcing yet.

## Other critical findings
- The available source baseline is LineageOS 19.1 / Android 12L generation, **not** LineageOS 23 / Android 16.
- The common configuration targets Linux 3.18.
- Legacy proprietary vendor blobs and HIDL HALs have not been shown compatible with Android 16.
- The pinned device tree sets `PRODUCT_SHIPPING_API_LEVEL := 23` (Android 6.0-era launch API), and its device VINTF manifest is HIDL-based with compatibility target level 3.
- The common board configuration is tied to Linux 3.18, sets `BOARD_VNDK_VERSION := current`, enables several legacy `BUILD_BROKEN_*` workarounds, and declares a vendor security patch level of `2020-12-05`.
- The current vendor package contains legacy Samsung/HIDL services and 32-bit proprietary libraries; Android 16 compatibility is unproven and will require explicit ABI, linker, HAL and VINTF validation.
- The legacy common BoardConfig declares 32 MiB boot, about 38 MiB recovery, about 2.67 GiB system and about 414 MiB vendor partitions. These are source configuration values, not verified live partition sizes for this exact handset; do not use them for a flashable image without device-side confirmation.
- The recovery fstab uses Samsung by-name partitions and `encryptable=footer` for userdata. This is a legacy layout reference only; encryption behavior and current recovery compatibility have not been tested on the handset.
- Exact SM-G610F partition/recovery layout and hardware functionality still require device-side verification.

## Current build state
- Android 16 / LineageOS 23 build: **not started**.
- Flashable ZIP: **not available**.
- Boot test on SM-G610F: **not performed**.
- GApps package: pending validation against the final Android 16 build and ABI.
- The existing GitHub Actions workflows audit source references and validate one kernel patch; they are **not ROM compilers**.

## Next required work
1. Identify a viable LineageOS 23 / Android 16 Exynos7870 platform base and all required dependency repositories.
2. Port the device/common trees and vendor integration to the Android 16 build system and framework interfaces.
3. Compile and validate the kernel and device SELinux policy; pass enforcing boot/AVC tests.
4. Validate partition sizes, recovery, and the exact SM-G610F variant.
5. Only after the source port is coherent, add an Android 16 build workflow and attempt a userdebug build.
6. Test boot and hardware functions before producing a release ZIP.

See `docs/LINEAGE23_PORTING_PLAN.md` and `docs/SELINUX_ENFORCING.md`. No ROM build or device test is claimed complete.

## Follow-up upstream Android 16 source audit (2026-11-13)
- Rechecked public GitHub repositories for an `on7xelte` / Exynos7870 Android 16 or LineageOS 23 device tree. No verifiable matching Android 16 device/common/vendor/kernel source set was found in the searches performed.
- Queried the live branch lists for all four pinned `Exynos7870-Revived-Beta` repositories. Device, common, and vendor trees expose only `lineage-19.1`; the kernel exposes `lineage-19.1`, `lineage-19.1-bpf`, `lineage-19.1-bpf-ksu`, and `lineage-19.1-nobpf`. No Android 16 / LineageOS 23 branch was present in those repositories at the time of inspection.
- The `Exynos7870-Revived-Beta` project still identifies its common device baseline as `lineage-19.1`.
- Additional public `on7xelte` trees inspected use older branches: `DevOtag-Open-Source/android_device_samsung_on7xelte` uses branch `twelve`; `Maxx12211/android_device_samsung_on7xelte` also uses branch `twelve`; `Exynos7870/android_device_samsung_on7xelte` defaults to `lineage-16.0`; `samsungexynos7870/android_device_samsung_on7xelte` defaults to `crdroid` and its board configuration inherits the legacy universal7870 common tree.
- A web article claims an unofficial LineageOS 23 beta for Exynos7870/on7xelte, but it does not provide independently verifiable source revisions or a release artifact in the article itself, and explicitly calls the build beta. It is not an acceptable foundation for this project's stable/security-focused release.
- Decision: do not switch the project to an unverified ZIP, do not re-label old trees as Android 16, and do not start a release build until a coherent Android 16 source manifest and exact SM-G610F compatibility evidence are available.
- Next gate: obtain and inspect the actual Android 16 device tree, common tree, kernel, vendor blobs and recovery/partition definitions; then validate build-system compatibility and security interfaces before enabling any ROM build workflow.

## SELinux source-level CI check (2026-11-13)
- Added `.github/workflows/selinux-kernel-patch-check.yml`.
- The workflow checks out the exact kernel revision pinned in the reference manifest, verifies that the stored patch applies cleanly, applies it in the CI workspace, and asserts that the patched defconfig keeps SELinux enabled while removing the two known defconfig bypasses.
- The first two workflow attempts failed due to malformed patch hunk metadata and then mismatched context. Both were corrected.
- Latest run: **success**, run ID `43374453805`, on commit `c94ff5ab796f5701f78d104fc636977cc0e321e3`. The patch application and defconfig assertions passed.
- This is a source-level patch check only. It does not compile the kernel or Android sepolicy, inspect the final boot image command line, boot the phone, or validate encryption/hardware. A passing run is not a ROM release approval.


## Additional Android 16 candidate verification (2026-11-13)
- GitHub repository search surfaced `ankursingh25643-crypto/Android16-on7xelte` (default branch `main`, latest inspected commit `6191ac34643316b6c04e5a8f01d8174bcddc6d0c`, last activity observed 2026-09-12).
- Inspected the repository tree and build files. It is only a small build-environment stub: `Device/Device` contains a placeholder README and no device source files; `Build.yml` installs host dependencies and the repo launcher but does not run `repo init`, `repo sync`, `lunch`, or an Android build. It is not a usable Android 16 source tree.
- The GETDroidTips article updated 2026-10-31 links an XDA thread titled as a beta LineageOS 23.2 / Android 16 Exynos7870 build: `https://xdaforums.com/t/beta-rom-16-0-exynos-7870-linageos-23-2-on7xelte.4803312/`. The article itself supplies no source revisions or downloadable ROM asset, calls the build beta, and points to XDA for current issues. The XDA page could not be retrieved during this audit. Treat this as an unverified lead only.
- Found public Linux kernel mailing-list patches for initial upstream Exynos7870 / on7xelte devicetree support. This is Linux mainline platform work, not an Android device tree, vendor blob set, Android HAL integration, recovery, or LineageOS 23 manifest.
- Added `docs/ANDROID16_SOURCE_AUDIT.md` with the candidate repository inspection, evidence, and blockers.
- Added `.github/workflows/android16-readiness-gate.yml` to recheck public Exynos7870 branch availability and reject the identified placeholder repository as a ROM source. This workflow audits sources only and intentionally does not launch a ROM build.
- Current decision remains **BLOCKED**: no reproducible Android 16 source set has passed inspection. No Android 16 build or flashable ZIP exists.
