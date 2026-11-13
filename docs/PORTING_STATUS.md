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

## Critical findings
- The available source baseline is LineageOS 19.1 / Android 12L generation, **not** LineageOS 23 / Android 16.
- The common configuration targets Linux 3.18.
- The kernel defconfig includes `CONFIG_CMDLINE="androidboot.selinux=permissive"`. This is not acceptable for a release ROM. SELinux enforcing requires a completed and tested policy; blindly changing this flag may break boot or hardware.
- Legacy proprietary vendor blobs and HIDL HALs have not been shown compatible with Android 16.
- Exact SM-G610F partition/recovery layout and hardware functionality still require device-side verification.

## Current build state
- Android 16 / LineageOS 23 build: **not started**.
- Flashable ZIP: **not available**.
- Boot test on SM-G610F: **not performed**.
- GApps package: pending validation against the final Android 16 build and ABI.
- The current GitHub Actions workflow is a source-reference audit only; it is not a ROM compiler.

## Next required work
1. Identify a viable LineageOS 23 / Android 16 Exynos7870 platform base and all required dependency repositories.
2. Port the device/common trees and vendor integration to the Android 16 build system and framework interfaces.
3. Resolve kernel compatibility, boot image/DTB handling, and SELinux enforcing.
4. Validate partition sizes, recovery, and the exact SM-G610F variant.
5. Only after the source port is coherent, add an Android 16 build workflow and attempt a userdebug build.
6. Test boot and hardware functions before producing a release ZIP.

See `docs/LINEAGE23_PORTING_PLAN.md` for details. No build or device test is claimed complete.
