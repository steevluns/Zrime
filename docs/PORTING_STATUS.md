# Porting status

## Target
- Device: Samsung Galaxy J7 Prime SM-G610F (`on7xelte`).
- Intended release: Android 16 / LineageOS 23.
- Requested userspace: ARM64, with the required 32-bit compatibility where device blobs need it.

## Source audit findings (2026-11-13)

A newer community source set was found under `Exynos7870-Revived-Beta`. It is materially newer than the older `Exynos7870` LineageOS 16.0 trees, but it is **not an Android 16 port**.

Verified files:
- `android_device_samsung_on7xelte`, branch `lineage-19.1`:
  - `lineage_on7xelte.mk` inherits `core_64_bit.mk`.
  - It sets `TARGET_GAPPS_ARCH := arm64`.
  - `BoardConfig.mk` selects `exynos7870-on7xelte_defconfig`.
  - The device tree inherits the shared `universal7870-common` configuration.
- `android_device_samsung_universal7870-common`, branch `lineage-19.1`:
  - `TARGET_ARCH := arm64`, `TARGET_CPU_ABI := arm64-v8a`.
  - It also defines a 32-bit secondary architecture (`armeabi-v7a` / `armeabi`).
  - `TARGET_USES_64_BIT_BINDER := true`.
  - Kernel source is configured as `kernel/samsung/exynos7870`, Linux 3.18.
- `android_vendor_samsung_on7xelte`, branch `lineage-19.1`:
  - A vendor makefile and proprietary blob list exist, including legacy 32-bit vendor libraries.

## Compatibility assessment

- ARM64 Android userspace: **supported by the inspected LineageOS 19.1 device/common build configuration**. This is stronger evidence than CPU capability alone, but does not prove an Android 16 build will boot.
- 32-bit compatibility: the shared build configuration declares a secondary 32-bit architecture. Keep this until every required vendor blob and app compatibility dependency is audited.
- Android 16 / LineageOS 23: **not verified**. The inspected device, common, and vendor trees are on `lineage-19.1` (Android 12L generation), not `lineage-23.0`.
- Kernel: the shared configuration targets Linux 3.18. The Android 16 compatibility of this kernel, its drivers, defconfig, DTB, and boot image is not established.
- Vendor/HAL: a legacy proprietary vendor set exists, but its VNDK/HAL compatibility with Android 16 is not established.
- Partition layout, recovery, firmware, camera, radio, Wi-Fi, fingerprint, SELinux policy, and first-boot behavior still require validation on the exact SM-G610F variant.

## Build status
- Android 16 build: not started.
- Flashable ZIP: not available.
- Device boot test: not performed.
- GApps: pending Android 16 and ABI compatibility verification.

## Required next engineering work

1. Pin exact commits for the 19.1 device/common/vendor/kernel sources and inspect their manifests and dependencies.
2. Verify the kernel repository's actual defconfig path, toolchain requirements, DTB handling, and boot image layout.
3. Port device makefiles, init, sepolicy, VINTF, overlays, HAL interfaces, and vendor integration to LineageOS 23.
4. Resolve Android 16 framework/VNDK incompatibilities; do not assume old proprietary blobs will work unchanged.
5. Confirm partition sizes and recovery compatibility for the exact SM-G610F variant.
6. Only then add a real Android 16 build workflow. A source-audit workflow is not a ROM build.

The existing CI workflow is a reference audit only. Passing it does not demonstrate buildability, bootability, or hardware functionality.
