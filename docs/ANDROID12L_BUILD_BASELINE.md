# Android 12L build baseline — SM-G610F / on7xelte

## Selection result

The highest Android version for which this audit found a complete, explicitly device-targeted and reproducible public source manifest is **Android 12L / LineageOS 19.1**.

- Android 16: no complete verified device/kernel/vendor source set for on7xelte was found.
- Android 15, 14 and 13: no complete verified source set for this exact device was found.
- Android 12L: the Exynos7870-Revived-Beta organization explicitly lists Galaxy J7 Prime / on7xelte and publishes a LineageOS 19.1 local manifest, device tree, common tree, kernel and vendor trees.

A newer LineageOS 22.2 release stream was found for other Exynos7870 devices (including j5y17lte and a3y17lte), but no on7xelte build or complete on7xelte 22.2 source manifest was verified. It is not safe to transplant those device trees to the J7 Prime.

## Pinned source family

Manifest: `manifests/7870.xml`

Platform:
- `https://github.com/LineageOS/android.git`
- branch: `lineage-19.1`
- Android generation: 12L

Device:
- `device/samsung/on7xelte`
- lunch target: `lineage_on7xelte-userdebug`

Supporting sources:
- `device/samsung/universal7870-common`
- `kernel/samsung/exynos7870`
- `vendor/samsung/on7xelte`
- `vendor/samsung/universal7870-common`
- Samsung hardware / SLSI Linaro repositories

The manifest tracks the upstream `lineage-19.1` branch. For a release build, pin every resolved project to immutable commit SHAs after the first successful sync and record them in a lock manifest.

## Release-artifact verification

The Exynos7870-Revived-Beta Releases repository was inspected directly. Its published LineageOS 19.1 release asset is for **j7xelte**, not **on7xelte**. The OTA JSON contains an on7xelte download URL, but that exact file was not present in the public Releases asset listing during this audit. Therefore, it is **not valid evidence of a downloadable, published on7xelte Android 12L ROM**.

The source trees and lunch target provide a candidate build baseline, but a successful on7xelte build has not yet been independently confirmed by a release asset. This raises the verification level required before any flashable package is trusted.

## Build runner requirements

The full Android source checkout and output are too large for a normal small GitHub-hosted runner. The build workflow therefore targets a dedicated self-hosted Linux x86_64 runner labelled `android-rom-builder` and checks for at least:
- 24 logical CPU threads
- 64 GiB RAM
- 400 GiB free workspace storage

If those requirements are not met, the workflow stops before syncing or building. Do not remove this check to force a partial or corrupted build.

## Current status

- Source family and local manifest: prepared.
- Build workflow: prepared, manual dispatch only.
- Full source sync/build: not yet run.
- ROM ZIP from Zrime: not yet produced.
- Boot, recovery, radio, camera, Wi-Fi, Bluetooth, encryption, SELinux enforcing and OTA tests on a physical SM-G610F: not completed.
- Dark theme and Google Apps: not yet integrated into the Zrime build. Google Apps must be installed from a separately obtained package compatible with Android 12L and the device ABI unless a legally redistributable solution is selected.

Do not flash a newly built package until its device assertions, partition compatibility, checksums, recovery installation and first boot have been verified on the exact handset.
