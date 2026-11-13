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
- **Not yet complete:** the patch has not been applied to a checked-out kernel build, the final Android 16 policy has not been ported/compiled, and no enforcing boot or AVC validation has been performed. Do not call the ROM SELinux-enforcing yet.

## Other critical findings
- The available source baseline is LineageOS 19.1 / Android 12L generation, **not** LineageOS 23 / Android 16.
- The common configuration targets Linux 3.18.
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
3. Apply and validate the kernel SELinux patch, port the device policy, and pass enforcing boot/AVC tests.
4. Validate partition sizes, recovery, and the exact SM-G610F variant.
5. Only after the source port is coherent, add an Android 16 build workflow and attempt a userdebug build.
6. Test boot and hardware functions before producing a release ZIP.

See `docs/LINEAGE23_PORTING_PLAN.md` and `docs/SELINUX_ENFORCING.md`. No build or device test is claimed complete.
