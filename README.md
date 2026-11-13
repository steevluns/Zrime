# Zrime — Samsung Galaxy J7 Prime (SM-G610F)

Target device: **Samsung Galaxy J7 Prime, codename `on7xelte`**.

## Selected Android version

The highest complete, device-specific source baseline verified for this exact device in this project is **Android 12L / LineageOS 19.1**.

Android 16, 15, 14 and 13 did not have a complete, reproducible set of on7xelte device, kernel and vendor sources during the source audit. Newer LineageOS 22.2 work was found for other Exynos7870 models, but an on7xelte 22.2 build was not verified. Zrime therefore moves to the verified Android 12L baseline rather than claiming unsupported newer compatibility.

## Source and build files

- Local manifest: [manifests/7870.xml](manifests/7870.xml)
- Source/build status: [docs/ANDROID12L_BUILD_BASELINE.md](docs/ANDROID12L_BUILD_BASELINE.md)
- Manual build workflow: [Build Zrime Android 12L](.github/workflows/build-android12l.yml)

The source set uses LineageOS 19.1 and the Exynos7870-Revived-Beta device, common, kernel, vendor and Samsung hardware repositories. Device lunch target: `lineage_on7xelte-userdebug`.

## Build runner

A complete Android build cannot reliably fit on a standard small GitHub-hosted runner. The workflow requires a dedicated self-hosted Linux x86_64 runner labelled `android-rom-builder`, with at least 24 CPU threads, 64 GiB RAM and 300 GiB free workspace storage. It is manual-dispatch only and stops if those requirements are not met.

## Release status

The Android 12L source manifest and manual build workflow have been added. **A Zrime ROM build has not yet completed and there is no Zrime flashable ZIP yet.** The upstream OTA JSON mentions an on7xelte package URL, but the exact file was not present in the upstream public Releases asset listing when checked. The published LineageOS 19.1 ZIP found there is for j7xelte, not on7xelte; do not use it on the J7 Prime.

Still required before release:
1. Complete source sync and successful ROM compilation.
2. Pin all synced source projects to immutable commit SHAs.
3. Review and validate kernel configuration, SELinux enforcing, VINTF/HAL compatibility and partition assertions.
4. Decide and test the dark theme and debloat set.
5. Integrate or document a separately obtained Android 12L-compatible Google Apps package.
6. Verify ZIP integrity, recovery installation and first boot on the exact SM-G610F.
7. Test calls/SMS/mobile data, Wi-Fi, Bluetooth, camera, GPS, sensors, audio, storage, encryption and SELinux enforcing.

Do not flash an untested image. Back up user data and original firmware first; an incompatible image can make the phone unbootable.
