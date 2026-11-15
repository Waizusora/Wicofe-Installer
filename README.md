# Wicofe-Installer

Installer and deployment workflow for Wicofe OS.

## Scope

- Installation environment and user workflow.
- Disk and partition selection.
- Bootloader installation and boot entry configuration.
- Base system deployment and initial configuration.
- Installation logs, validation, and failure recovery.

## Safety requirements

Show the selected target device and planned destructive operations before execution. Validate device identity and capacity, handle power loss and partial failure, and avoid implicit formatting. Installation must be reproducible from a declared system image and manifest.

## Boundaries

Wicofe-Image produces system images; Wicofe-Storage provides storage tools; Wicofe-Update handles in-place system updates; Wicofe-Recovery owns repair and recovery workflows.

## Status

Initial repository baseline; installation format and workflow are not yet implemented.

## License

Original project code targets MPL-2.0; dependencies require individual review.
