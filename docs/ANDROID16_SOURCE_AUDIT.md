# Android 16 source candidate audit — on7xelte

Date: 2026-11-13  
Target: Samsung Galaxy J7 Prime SM-G610F (on7xelte)  
Required platform: Android 16 / LineageOS 23

## Executive result

**PORT IS BLOCKED. No verified Android 16 device source set is available in the repositories examined.** No ROM build was started and no flashable package was produced.

## Candidate discovered: ankursingh25643-crypto/Android16-on7xelte

Repository: https://github.com/ankursingh25643-crypto/Android16-on7xelte  
Default branch: main  
Latest inspected commit: 6191ac34643316b6c04e5a8f01d8174bcddc6d0c  
Last repository activity observed: 2026-09-12

This repository is **not an Android 16 device source tree**:
- Repository size reported by GitHub: 7 KB.
- Root contains Build.yml, README.md, a Device directory and .github.
- The Device/Device subtree contains only a one-byte README.md; no Android device makefiles, BoardConfig, init, fstab, VINTF, sepolicy, kernel source or vendor blobs were found.
- Build.yml installs basic host dependencies and the repo launcher, then creates an empty android-source directory. It does not initialize or sync an Android manifest, select a lunch target, compile Android, or produce an APK/ZIP.
- .github/workflows/build.yml is only a short environment-preparation workflow, not a ROM build pipeline.

Therefore, this repository cannot be used as an Android 16 source manifest or as proof that a LineageOS 23 ROM exists.

## XDA / download-article claim

A GETDroidTips page updated 2026-10-31 describes an unofficial LineageOS 23.2 beta for Exynos 7870 and links an XDA thread:
https://xdaforums.com/t/beta-rom-16-0-exynos-7870-linageos-23-2-on7xelte.4803312/

The article itself does not provide source revisions or a ROM asset directly, labels the build beta, and refers readers to the XDA thread for the real issue/working list. The XDA page could not be retrieved during this audit. This claim is a lead for further verification, not a reproducible source set and not release evidence.

## Existing Exynos7870-Revived-Beta baseline

The project's documented source set remains LineageOS 19.1 / Android 12L:
- device tree: android_device_samsung_on7xelte
- common device tree: android_device_samsung_universal7870-common
- vendor tree: android_vendor_samsung_on7xelte
- kernel: android_kernel_samsung_exynos7870
- manifest and Samsung hardware interfaces also expose only lineage-19.1 in the branch checks performed.

This is the pinned legacy reference baseline only. It must not be relabeled Android 16.

## Mainline Linux work is not an Android ROM port

Public Linux kernel mailing-list patches add initial upstream devicetree support for samsung,on7xelte / Exynos7870. This is useful kernel-platform work, but it does not supply Android device configuration, proprietary vendor blobs, Samsung Android HAL integration, recovery, Android SELinux policy, or a LineageOS 23 manifest. It cannot replace the Android device/vendor source set.

## Engineering blockers

1. A coherent LineageOS 23 manifest and matching device/common/vendor/kernel revisions are missing.
2. Legacy device sources declare Android 6-era shipping API, HIDL interfaces and old vendor dependencies.
3. Legacy common configuration targets Linux 3.18 and carries a 2020 vendor security patch level.
4. Proprietary 32-bit/64-bit blob dependencies, linker namespaces and Android 16 VNDK compatibility are unverified.
5. Full SELinux policy port and enforcing boot are not validated.
6. Exact SM-G610F partition sizes, boot/recovery image format, firmware and encryption behavior need on-device confirmation.
7. Hardware validation is still required for radio, Wi-Fi, Bluetooth, camera, audio, GPS, fingerprint, sensors, charging, storage and recovery.

## Gate decision

- Do not start an Android 16 release build from the current repositories.
- Do not flash an unverified beta or a package for another Exynos7870 model.
- Keep the successful SELinux kernel patch check classified as a source-level check only.
- Resume ROM build work only after a real, inspectable Android 16 source manifest and all required device dependencies are pinned and pass compatibility checks.

## Evidence links

- Exynos7870-Revived-Beta project: https://github.com/Exynos7870-Revived-Beta
- Candidate Android16-on7xelte repository: https://github.com/ankursingh25643-crypto/Android16-on7xelte
- XDA thread referenced by the article: https://xdaforums.com/t/beta-rom-16-0-exynos-7870-linageos-23-2-on7xelte.4803312/
- Initial upstream Linux devicetree support discussion: https://lists.infradead.org/pipermail/linux-arm-kernel/2025-April/1018496.html
