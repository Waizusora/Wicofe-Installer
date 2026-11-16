# Wicofe Installation and Deployment Architecture 0.1

**Status:** Draft proposal. This document defines installation and first-deployment boundaries. It does not specify a finalized disk layout, partition table, boot protocol, or executable installer implementation.

## 1. Purpose

Wicofe-Installer deploys a selected Wicofe system image or installation payload onto a target device and performs first-boot preparation. It owns the interactive and automated installation workflow, target-device safety, deployment orchestration and installation result reporting.

It must make destructive effects explicit, validate the target immediately before writing, and recover safely from interruption.

## 2. Ownership boundaries

| Repository | Canonical responsibility |
|---|---|
| Wicofe-Installer | Installation UI/workflow, target selection, preflight, deployment, boot entry setup, initial configuration and install result |
| Wicofe-Image | Source system image, image manifest, partition/filesystem/boot artifact composition and image validation |
| Wicofe-Storage | Storage discovery, device identity, partition inspection and storage operations exposed to userspace |
| ONWFS | ONWFS format, formatter, checker and filesystem-specific semantics |
| Wicofe-Kernel | Kernel boot/runtime and device interfaces |
| Wicofe-Update | In-place installed-system update and activation/rollback |
| Wicofe-Recovery | Repair, restore and recovery environment |
| WPM | Individual package installation and package database |
| Wicofe-Tests | Installation, destructive-operation, boot and recovery integration suites |

Installer orchestrates installation but does not own filesystem formats, package dependency resolution, release image construction or subsequent OS update transactions.

## 3. Installation modes

Potential modes, subject to implementation and validation:
- **Full-disk installation:** replace the selected target layout and deploy a complete system image.
- **Manual/custom layout:** allow explicit partition selection and mount/format choices once layout policy is defined.
- **Image deployment:** write a validated disk image to a target device.
- **Existing-system migration:** not supported until data migration, boot coexistence and rollback semantics are specified.

Do not advertise a mode until its layout, failure recovery and test coverage are implemented. Dual boot, encryption, secure boot and non-destructive resizing remain explicit future decisions rather than implied capabilities.

## 4. Preflight and target identification

Before any destructive action:
- enumerate candidate devices through Wicofe-Storage;
- identify devices using stable attributes where available (hardware identity, serial, WWN, device topology and capacity);
- distinguish removable media, system/boot devices and mounted/in-use volumes;
- detect mounted filesystems, swap, active device mappings and holders;
- check capacity, logical sector size, alignment constraints and write capability;
- validate architecture, firmware/boot mode and image compatibility;
- verify that the selected image manifest and all referenced artifacts are present and valid;
- ensure the target is not the installer source or an active system device unless the operation is explicitly designed and validated for it.

Device paths such as /dev names are transient and must not be treated as durable identity. Re-resolve and compare device identity immediately before the first write and before each later destructive phase.

## 5. Destructive-operation consent

Present a clear installation plan before execution:
- exact target identity, model/capacity and current device path;
- selected image/release and architecture;
- partition table and proposed partition changes;
- filesystems to be created or overwritten;
- whether existing data will be destroyed;
- bootloader/firmware changes;
- expected duration and recovery limitations.

Require explicit confirmation tied to the displayed plan. If target identity, image, layout or destructive scope changes, invalidate the confirmation and request it again. No implicit format, partition-table rewrite or device wipe is permitted.

The confirmation UI must not obscure critical target information and should remain accessible to assistive technologies.

## 6. Deployment transaction

Model installation as explicit phases with durable progress records:

1. **Preflight:** validate image, target, power/storage conditions and compatibility.
2. **Plan:** resolve the complete partition/filesystem/write plan without modifying the target.
3. **Consent:** obtain explicit approval for the exact destructive plan.
4. **Revalidate:** re-identify target and compare its current state to the approved plan.
5. **Prepare:** create or update partition layout and required filesystems according to the selected profile.
6. **Deploy:** write/copy verified image payloads and required boot artifacts.
7. **Configure:** write system identity and initial configuration without embedding secrets in logs.
8. **Boot setup:** install/configure the boot path supported by the selected layout and firmware.
9. **Verify:** read back critical metadata, validate filesystem/partition state and check required files.
10. **Finalize:** write completion marker and produce an installation report.

Every phase must declare its idempotency and recovery behavior. Persist progress safely enough to distinguish completed, incomplete and indeterminate operations after a crash or power loss.

## 7. Power-loss and failure safety

A disk installation cannot always be rolled back after overwriting the target. Do not promise atomic rollback for arbitrary full-disk writes.

Requirements:
- keep the source image and installation environment separate from the target;
- verify source artifacts before destructive writes;
- order writes so that a failed operation leaves a diagnosable state;
- use flush/barrier semantics where the device supports them;
- detect short writes, I/O errors, disconnects and capacity changes;
- retain a bounded installation journal/report on a safe location that is not being overwritten;
- never mark installation complete before all required verification passes;
- on restart, re-identify the target and inspect actual on-disk state rather than trusting only the last progress record.

If failure leaves the target partially deployed, report it as incomplete/indeterminate and provide a recovery path. Do not automatically retry destructive operations against a device whose identity or state changed.

## 8. Boot configuration and first boot

Bootloader and firmware setup must follow a versioned layout profile supplied by Wicofe-Image and validated with Wicofe-Kernel. The installer must not invent partition identifiers, boot paths or firmware assumptions.

First-boot tasks may include machine identity generation, initial user setup, locale/time configuration and device-specific configuration. Define which tasks run in the installer and which are delegated to first-boot services. Generate unique secrets/identifiers at the correct stage; never clone machine-specific credentials from a generic image.

## 9. Security and privacy

- Run privileged storage operations through a narrow, auditable interface.
- Authenticate and authorize every destructive operation.
- Validate all image, archive, path, offset and length inputs.
- Avoid shelling out with interpolated untrusted input.
- Keep logs free of passwords, recovery keys and authentication tokens.
- Minimize retention of hardware identifiers and user-entered personal data.
- Verify release authenticity according to the image signing/trust policy.
- Do not disable platform protections silently.
- Treat installer media and network sources as untrusted until verified.

## 10. User experience and accessibility

Show progress by phase, not an arbitrary percentage when total work is unknown. Distinguish warnings, recoverable errors and irreversible failures. Provide a readable installation summary with target, release, layout profile, completion state and diagnostic reference.

Support keyboard navigation, screen-reader labels, high contrast and clear focus indication. Network-dependent options must explain required connectivity and allow offline installation when all required artifacts are locally available.

## 11. Testing strategy

- Dry-run plan generation with no device writes.
- Device identity change between plan, consent and execution.
- Wrong-target, installer-source and mounted-device rejection.
- Capacity, sector-size and alignment boundary tests.
- Corrupt/truncated image and hash/signature mismatch tests.
- Short write, I/O error, device disconnect and power-loss injection.
- Restart/recovery at every deployment phase.
- Partition and filesystem post-write verification.
- Boot tests for each claimed architecture/firmware profile.
- First-boot uniqueness and secret-handling tests.
- Accessibility and keyboard-only workflow tests.
- Destructive tests only on disposable virtual disks or dedicated test hardware.

## 12. Open decisions

- Supported installation media and runtime environment.
- Full-disk versus custom/manual layout scope for initial release.
- Versioned partition/boot layout profile and firmware matrix.
- Image deployment versus file-level installation.
- Filesystem creation and validation tool contracts.
- Installer progress journal format and safe storage location.
- Bootloader installation and NVRAM update policy.
- Encryption, secure boot, dual boot and recovery partition support.
- Network installation and offline artifact cache policy.
- First-boot responsibility split with Osynit and Wicofe-Services.

## 13. Completion criteria

A production installer requires a frozen image/layout contract, stable target identification, explicit destructive consent, verified inputs, tested interruption handling, post-write verification, boot validation for every claimed target, accessible workflow and documented recovery limitations. Until then, this architecture remains a draft.
