# SELinux enforcing work — on7xelte

## Findings in the pinned reference kernel

The pinned Exynos7870 kernel revision
`455cdd6861c6137c4205124c279c54829e3cd6e6` has two independent
SELinux enforcement bypasses in
`arch/arm64/configs/exynos7870-on7xelte_defconfig`:

- `CONFIG_CMDLINE="androidboot.selinux=permissive"` injects a permissive
  boot argument.
- `CONFIG_SECURITY_SELINUX_NEVER_ENFORCE=y` compiles out the ability to
  switch SELinux into enforcing mode.

Changing only the boot argument is insufficient while
`NEVER_ENFORCE` remains enabled. The kernel baseline patch in
`patches/kernel/0001-on7xelte-selinux-enforcing-baseline.patch` removes
both bypasses. It leaves `CONFIG_SECURITY_SELINUX=y`,
`CONFIG_SECURITY_SELINUX_BOOTPARAM=y`, and the enforcing default value
enabled.

## Device policy already wired by the reference tree

The common device BoardConfig includes
`device/samsung/universal7870-common/sepolicy_slsi_exynos7870/sepolicy.mk`
and adds `device/samsung/universal7870-common/sepolicy/vendor` through
`BOARD_SEPOLICY_DIRS`. Keep both policy sources in the port; do not replace
them with blanket allow rules.

## Required validation before enabling enforcing in a ROM

1. Apply the kernel patch to the pinned kernel source and verify the generated
   `.config` has `CONFIG_SECURITY_SELINUX=y`,
   `CONFIG_SECURITY_SELINUX_NEVER_ENFORCE` unset, and no permissive
   `CONFIG_CMDLINE` argument.
2. Search bootloader, boot image command line, init rc files, and product
   properties for any remaining `androidboot.selinux=permissive` or
   `selinux=0` override. The effective kernel command line must not request
   permissive mode.
3. Build the policy with the target Android branch's sepolicy compiler.
   Resolve duplicate/unknown types, neverallow violations, and policy version
   errors. Do not silence neverallow checks.
4. Boot a userdebug image on the exact SM-G610F variant with enforcing
   enabled. Confirm `getenforce` reports `Enforcing` and inspect
   `dmesg`, logcat, and kernel audit output for AVC denials.
5. Exercise boot, encryption, USB/MTP, ADB, Wi-Fi, Bluetooth, RIL, audio,
   camera, GPS, sensors, fingerprint, storage, and power management. Add only
   narrow, evidence-based rules for legitimate denials, then repeat tests.
6. Test a clean boot and reboot cycle, and verify enforcing remains active
   after reboot and recovery/OTA paths.

## Current limitation

This repository contains a kernel configuration patch and validation plan,
not a completed Android 16 SELinux policy port. The available device/common
policy baseline belongs to LineageOS 19.1 / Android 12L. It must be audited
and ported for the eventual target branch; it is not evidence that Android 16
will boot enforcing today.

**No enforcing boot test has been performed. Do not label the ROM enforcing
or release-ready until the checks above pass on hardware.**
