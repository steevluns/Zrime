# Android 12L build baseline — SM-G610F / on7xelte

## Selected source baseline

The project targets **Android 12L / LineageOS 19.1**. The upstream Exynos7870-Revived-Beta organization explicitly lists Galaxy J7 Prime / `on7xelte` and publishes the matching device tree, common tree, kernel, vendor trees and a LineageOS 19.1 local manifest.

The upstream manifest was fetched and compared with this project's manifest. Zrime now tracks the full upstream project set and additionally includes `android_packages_resources_devicesettings`, which is referenced by `device/samsung/universal7870-common/lineage.dependencies`.

## Device configuration reviewed

The on7xelte device tree exposes:
- `lineage_on7xelte-userdebug` in `AndroidProducts.mk`.
- `PRODUCT_DEVICE := on7xelte` and `TARGET_GAPPS_ARCH := arm64`.
- Board assertion list for the on7xelte family.
- Kernel defconfig `exynos7870-on7xelte_defconfig`.
- Vendor makefile and proprietary blob list.
- A common device dependency and a common resources dependency.

The platform is 64-bit ARM64 with 32-bit secondary ABI support. It is not an ARM32-only Android build.

## Zrime changes wired into the build

1. Device resource overlay sets `config_defaultNightMode=2` so a clean first boot defaults to dark mode; the user can still change it.
2. The kernel patch removes `androidboot.selinux=permissive` from the kernel command line and disables `CONFIG_SECURITY_SELINUX_NEVER_ENFORCE`.
3. The workflow applies the two upstream patches documented as active by the Exynos 7870 patch repository, with strict apply-or-already-applied checks.
4. The workflow records an immutable `repo manifest -r` lock file in the build artifact.
5. The workflow verifies the produced ZIP archive and publishes SHA-256 checksums.

These steps improve build preparation but do not prove that the kernel boots, SELinux reaches Enforcing, or hardware works.

## Google Play / GApps

Google Play Services and Play Store are proprietary Google components. They are not redistributed inside the Zrime source repository. The build documentation directs the user to flash a separately obtained package specifically compatible with Android 12L and ARM64 after the ROM, using that package's official instructions. Play Store installation is not equivalent to Play Protect certification or Play Integrity passing.

## Build host requirements

The workflow targets a dedicated self-hosted Linux x86_64 runner labelled `android-rom-builder` and checks for:
- At least 24 logical CPU threads.
- At least 64 GiB RAM.
- At least 400 GiB free workspace storage.

A standard small GitHub-hosted runner does not meet these requirements. The workflow is manual-dispatch only and will remain queued until a matching runner is online.

## Current status and release gates

- Upstream source manifest: reviewed and aligned.
- Device product files, common dependencies and kernel defconfig: inspected.
- Dark-mode resource overlay: patch prepared for the build.
- SELinux defconfig hardening: patch prepared for the build.
- Upstream platform compatibility patches: wired into the build workflow.
- Full source sync: not run.
- Full Android compilation: not run.
- Zrime flashable ZIP: not produced.
- Recovery installation and first boot on physical SM-G610F: not tested.
- Calls, SMS, mobile data, Wi-Fi, Bluetooth, camera, GPS, audio, fingerprint, sensors, charging, USB/MTP, storage, encryption and SELinux enforcing: not hardware-validated.

Do not label or flash a package as stable, secure or daily-driver ready until the clean build, archive checks, recovery install and physical-device tests have passed.
