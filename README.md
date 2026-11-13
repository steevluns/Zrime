# Zrime — Samsung Galaxy J7 Prime (SM-G610F)

Target device: **Samsung Galaxy J7 Prime, codename `on7xelte`**.

## ROM target

- Android base: **Android 12L / LineageOS 19.1**.
- Device lunch target: `lineage_on7xelte-userdebug`.
- CPU ABI: ARM64 (with 32-bit ARM compatibility libraries); do not confuse this with a 32-bit-only device.
- UI: default dark mode is added as a device resource overlay; users can still switch themes in Settings.
- SELinux: the kernel defconfig patch removes the permissive boot argument and `CONFIG_SECURITY_SELINUX_NEVER_ENFORCE`. This is only a source-level hardening step, not proof of enforcing runtime.
- Google Play: Google proprietary apps are not bundled in this repository. Install a separately obtained, Android 12L-compatible ARM64 GApps package after the ROM, following that package's instructions. Play Store presence does not imply Play Protect certification or Play Integrity success.

## Source and build files

- Local manifest: [manifests/7870.xml](manifests/7870.xml)
- Build baseline and remaining release gates: [docs/ANDROID12L_BUILD_BASELINE.md](docs/ANDROID12L_BUILD_BASELINE.md)
- Manual build workflow: [.github/workflows/build-android12l.yml](.github/workflows/build-android12l.yml)

The manifest follows the upstream Exynos7870-Revived-Beta LineageOS 19.1 source set and includes the device-specific, common, kernel, vendor, Samsung hardware and device-settings projects needed by the target.

## Build runner

A complete Android build requires a dedicated self-hosted Linux x86_64 runner labelled `android-rom-builder`, with at least 24 logical CPU threads, 64 GiB RAM and 400 GiB free workspace storage. The workflow checks these limits before syncing/building. It cannot run on a standard small GitHub-hosted runner.

## Release status

**No Zrime flashable ZIP has been built or hardware-tested yet.** The build workflow is prepared but requires the dedicated runner. The upstream release ZIP found in the public Releases page is for `j7xelte`, not `on7xelte`; never flash that package on the J7 Prime.

Before calling a build installable, it must complete a clean source sync and compilation, pass package and checksum checks, and be tested on the exact SM-G610F for recovery installation, first boot, calls/SMS/mobile data, Wi-Fi, Bluetooth, camera, GPS, audio, fingerprint, sensors, charging, USB/MTP, storage, encryption and SELinux enforcing. Back up user data and stock firmware before any future test flash.
