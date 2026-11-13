# Zrime release security gate — SM-G610F (on7xelte)

**Release status: BLOCKED — no installable release is approved.**

The current target is LineageOS 19.1 / Android 12L. The repository contains build configuration and source patches, not a completed ROM image.

## Mandatory evidence before release

### Source and reproducibility
- Complete a clean source sync and build.
- Preserve the immutable `repo manifest -r`, build logs, toolchain versions and SHA-256 checksums.
- Audit proprietary blobs, licensing, ELF architecture, dependencies and vendor interface compatibility.

### Kernel and SELinux
- Verify the final generated kernel configuration: SELinux enabled, `CONFIG_SECURITY_SELINUX_NEVER_ENFORCE` disabled, and no permissive kernel or bootloader command-line override.
- Compile target-branch sepolicy without suppressing neverallow checks.
- Boot the exact SM-G610F with SELinux enforcing; record `getenforce = Enforcing` and review AVC denials. A source patch alone is not sufficient.

### User data and hardware-backed security
- Verify file-based encryption on the actual userdata filesystem, including credential-encrypted data remaining unavailable until first unlock.
- Verify Keymaster/Gatekeeper integration and hardware-backed key storage where supported. Do not claim hardware-backed security if the device falls back to software.
- Do not claim stock-equivalent Verified Boot on an unlocked custom-ROM boot chain. Do not relock the bootloader without proving the complete device-specific signing and rollback chain.

### Device functionality
Test calls, SMS, mobile data, Wi-Fi, Bluetooth, GPS, both cameras, audio, fingerprint, sensors, charging, USB/MTP, storage, recovery and repeated cold boots on the exact SM-G610F variant.

### Google services and release claims
- Obtain Google apps separately from a legitimate source and use a package compatible with Android 12L and ARM64.
- Play Store presence does not imply Play Protect certification or Play Integrity success.
- Never falsify the Android security patch level.
- Publish checksums, known limitations, exact supported model, installation and rollback instructions.

Until all applicable gates have recorded passing evidence on the target device, do not label the build stable, secure, daily-driver ready or suitable for primary/banking accounts.
