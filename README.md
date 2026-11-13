# J7 Prime — Android 16 (LineageOS 23) port

Target device: **Samsung Galaxy J7 Prime (SM-G610F), codename `on7xelte`**.

This repository is the starting point for a source-porting effort. It is **not a ROM build yet**, and it does not contain a flashable ZIP.

## Target goals

- Android 16 / LineageOS 23, subject to device-source and hardware compatibility.
- A lightweight system image and a consistent dark theme.
- Remove nonessential preinstalled apps while retaining essential telephony, camera, storage, Bluetooth, recovery and security components.
- Keep Google Play services and Play Store support. The exact GApps package must match the verified device ABI and Android release; it may be distributed separately to keep the ROM smaller.
- Preserve a safe, reversible build process and publish logs/artifacts only after a successful build.

## Important compatibility gate

A 64-bit-capable CPU does **not** prove that this device can run a 64-bit Android userspace. Before selecting ARM64, the port must verify the boot image/kernel, vendor blobs, HALs, partition layout and existing device configuration. Do not flash an image until this is confirmed.

The historical on7xelte device/vendor sources known to this project target older LineageOS branches. They cannot be treated as Android 16-ready without substantial porting and testing.

## GitHub Actions

The workflow in `.github/workflows/device-source-audit.yml` is a **source audit only**. It checks the availability of known legacy source branches and uploads a report. It does not compile Android or create a flashable ZIP.

A full LineageOS build requires compatible Android 16 device, kernel and vendor trees, a matching manifest, and substantial RAM, storage and build time. Standard GitHub-hosted runners may be insufficient.

## Next engineering steps

1. Audit device tree, kernel and proprietary vendor sources.
2. Establish Android 16 compatibility and the actual kernel/userspace ABI.
3. Port and validate device configuration, sepolicy, HALs and proprietary blobs.
4. Build and boot-test a minimal image on the exact device.
5. Add dark-theme overlays and a reviewed debloat list.
6. Integrate compatible GApps only after ABI validation, then produce and test a recovery-installable package.

## Release status: blocked

This project is not currently a complete, stable, or security-validated ROM. Do not flash it or use it with primary accounts. The available source baseline is LineageOS 19.1 / Android 12L generation, not Android 16; no Android 16 build, flashable ZIP, enforcing boot, encryption validation, or hardware test has been completed. A release is prohibited until every applicable check in [docs/SECURITY_RELEASE_GATE.md](docs/SECURITY_RELEASE_GATE.md) passes on the exact SM-G610F device.

**Safety:** Back up all data and the original firmware. An untested or mismatched image can make the device unbootable.