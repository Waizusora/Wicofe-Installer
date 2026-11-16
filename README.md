# Wicofe-Installer

Installer and deployment workflow for Wicofe OS.

## Scope

- Installation environment and user workflow.
- Disk and partition selection.
- Bootloader installation and boot entry configuration.
- Base system deployment and initial configuration.
- Installation logs, validation, and failure recovery.

## Planned user entry point

The intended installation environment starts in a text-based CLI and launches the installer when the user enters `wicofe-install`. This is a planned user experience only; the command and end-to-end install-to-first-boot workflow are not implemented or verified. See the [system delivery and installation direction](https://github.com/Waizusora/Wicofe-Docs/blob/main/SYSTEM_DELIVERY_AND_INSTALLATION_DIRECTION_0.1.md).

## Safety requirements

Show the selected target device and planned destructive operations before execution. Validate device identity and capacity, handle power loss and partial failure, and avoid implicit formatting. Installation must be reproducible from a declared system image and manifest.

## Boundaries

Wicofe-Image produces system images; Wicofe-Storage provides storage tools; Wicofe-Update handles in-place system updates; Wicofe-Recovery owns repair and recovery workflows.

## Status

Initial repository baseline; installation format and workflow are not yet implemented.

## License

Original project code targets MPL-2.0; dependencies require individual review.

## Documentation

See [docs/INDEX.md](docs/INDEX.md) for this repository's technical documentation. The [central repository documentation map](https://github.com/Waizusora/Wicofe-Docs/blob/main/REPOSITORY_DOCUMENTATION_MAP.md) identifies canonical ownership and cross-repository boundaries.
