# Zrime release security gate — SM-G610F (on7xelte)

This gate is mandatory. A source archive, successful compile, emulator boot, or permissive boot is not a releasable ROM.

## Release status

**BLOCKED — no installable release is approved.**

The repository currently contains a LineageOS 19.1 / Android 12L-era reference source set and porting notes. It does not contain a complete LineageOS 23 / Android 16 device port, a tested recovery package, or a verified release image.

## Mandatory evidence before a stable release

### Source and reproducibility
- Pin a coherent Android 16 manifest and every device, common, kernel, vendor, firmware and recovery dependency to immutable revisions.
- Build from a clean checkout in a reproducible environment; retain build logs, manifest lock data, checksums and toolchain versions.
- Audit proprietary blobs, licensing, ELF architecture, dependencies and vendor interface compatibility. Do not silently substitute incompatible blobs.

### Kernel and SELinux
- Apply the kernel enforcement patch to the exact kernel revision used by the release.
- Verify the final generated kernel config, not just the defconfig: SELinux enabled, CONFIG_SECURITY_SELINUX_NEVER_ENFORCE absent, and no permissive kernel or bootloader command-line override.
- Compile the target-branch sepolicy without suppressing neverallow checks.
- Boot the exact SM-G610F hardware with SELinux enforcing; record getenforce = Enforcing and audit AVC denials. Fix only narrow, justified policy denials.

### User-data protection and hardware-backed keys
- Verify file-based encryption on the actual userdata filesystem, including CE data remaining unavailable until first user unlock.
- Verify Keymaster/KeyMint and Gatekeeper integration, secure key storage in the TEE, and hardware-backed attestation where supported. Do not claim hardware-backed protection if it falls back to software.
- Verify lock-screen credential flows, reboot, recovery, backup/restore and data migration.
- Do not claim stock-equivalent Verified Boot on an unlocked custom-ROM boot chain. Do not relock the bootloader unless the complete device-specific verified boot chain and signing/rollback behavior have been proven.

### Device functionality and release quality
- Test calls, mobile data, SMS, Wi-Fi, Bluetooth, GPS, cameras, audio, fingerprint, sensors, charging, USB/MTP, storage, recovery and repeated cold boots on the exact SM-G610F variant.
- Run applicable Android compatibility/security tests (CTS/VTS where feasible), encryption tests and regression tests.
- Verify security patch provenance and a sustainable update process; never falsify the Android security patch level.
- Confirm Play Store installation separately from Play Protect certification and Play Integrity status. Do not promise banking-app compatibility or integrity-passing behavior.
- Publish checksums, known limitations, exact supported model, installation/rollback instructions and recovery steps.

## User safety rule

Until every applicable gate above has recorded passing evidence on the target device, do not label the build stable, secure, daily-driver ready, or suitable for primary/banking accounts. Do not publish or recommend flashing a test build as a finished ROM.