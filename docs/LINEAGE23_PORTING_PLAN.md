# LineageOS 23 porting plan — on7xelte

## Baseline identified
The current public community baseline is the `lineage-19.1` branch family in `Exynos7870-Revived-Beta`. It contains device, common, vendor, and kernel repositories for the Exynos 7870 platform. A reference manifest is stored at `manifests/lineage-19.1-reference.xml`.

This is a source baseline, not an Android 16 manifest. Do not sync it as if it were a LineageOS 23 target and do not publish its output as an Android 16 ROM.

## Confirmed hardware/build configuration
- Device: Samsung Galaxy J7 Prime, codename `on7xelte`.
- SoC: Exynos 7870; Cortex-A53.
- The common board configuration declares 64-bit ARM userspace (`arm64-v8a`) and a 32-bit secondary ABI (`armeabi-v7a`, `armeabi`).
- The device product inherits `core_64_bit.mk` and declares `TARGET_GAPPS_ARCH := arm64`.
- The shared board configuration targets Linux 3.18 and kernel source path `kernel/samsung/exynos7870`.
- The device tree selects `exynos7870-on7xelte_defconfig`; that file exists at `arch/arm64/configs/exynos7870-on7xelte_defconfig`.

## Blocking risks
1. The identified sources are LineageOS 19.1 generation, not LineageOS 23 / Android 16.
2. The kernel defconfig contains `CONFIG_CMDLINE="androidboot.selinux=permissive"`. This is not acceptable for a release build. SELinux enforcing must be made to work through a complete policy and device validation; simply flipping the flag without fixing policy can prevent boot or break hardware.
3. The device tree uses legacy HIDL interfaces and proprietary vendor blobs. Android 16 framework/VNDK compatibility is unproven.
4. Partition sizes, recovery compatibility, firmware, boot image/DTB format, and exact SM-G610F regional variant must be verified on-device.
5. Kernel 3.18 is old and may require substantial compatibility work for a modern Android framework.

## Engineering sequence
1. Treat the 19.1 source set as a reference baseline and pin exact source revisions before reproducible builds.
2. Establish a LineageOS 23 manifest and identify compatible common Exynos7870 platform repositories.
3. Port device makefiles, product configuration, overlays, init scripts, fstab, VINTF, sepolicy, and HAL declarations.
4. Audit every proprietary ELF blob for architecture, dependencies, linker namespaces, VNDK/API assumptions, and required 32-bit support.
5. Port or replace legacy kernel interfaces as needed; validate boot image, DTB, modules, and required Android kernel features.
6. Move SELinux to enforcing only after policy validation and successful boot tests.
7. Build a userdebug image, inspect logs, and test display, touch, storage, Wi-Fi, Bluetooth, audio, cameras, GPS, RIL, fingerprint, sensors, charging, and recovery.
8. Generate a flashable package only after device-specific validation and recovery/partition checks.

## Build policy
No Android 16 build workflow should be presented as functional until the LineageOS 23 source manifest and all required project revisions are available. The current audit workflow only checks source references and uploads a report; it does not compile Android.
