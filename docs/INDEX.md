# Installer Documentation Index

This directory documents installation and first-deployment workflows for Wicofe OS.

## Architecture
- [Installation and deployment architecture 0.1](INSTALLATION_DEPLOYMENT_ARCHITECTURE_0.1.md) — draft design for preflight, target identity, destructive consent, deployment phases, boot setup and interruption safety.
- [OSynit first-boot integration 0.1](OSYNIT_FIRST_BOOT_INTEGRATION_0.1.md) — init handoff validation and first-boot ownership.
- [OSynit first-boot integration 0.1](OSYNIT_FIRST_BOOT_INTEGRATION_0.1.md) — validates init handoff and assigns first-boot service ownership.

## Scope
- Installation environment and user workflow.
- Disk/partition selection and destructive-operation confirmation.
- Bootloader installation and boot-entry configuration.
- Base-system deployment, first-boot preparation, logs and failure handling.

## Ownership
[Wicofe-Image](https://github.com/Waizusora/Wicofe-Image) produces system images. [Wicofe-Storage](https://github.com/Waizusora/Wicofe-Storage) provides storage utilities. [Wicofe-Update](https://github.com/Waizusora/Wicofe-Update) manages in-place system updates. [Wicofe-Recovery](https://github.com/Waizusora/Wicofe-Recovery) owns recovery workflows.

## Safety
Never format or repartition implicitly. Show the exact target, proposed layout and data-loss consequences before execution. Revalidate device identity immediately before writes and define interruption/recovery behavior. A partially overwritten disk cannot be assumed to be automatically rollbackable.

- [Central documentation index](https://github.com/Waizusora/Wicofe-Docs/blob/main/INDEX.md)
- [Repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md)
