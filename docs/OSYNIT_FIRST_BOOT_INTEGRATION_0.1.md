# OSynit First-Boot Integration 0.1

- **Status:** Proposed installation integration
- **Owner:** Wicofe-Installer for deployment; OSynit for init and service lifecycle
- **Date:** 2026-11-16

## 1. Responsibility boundary

Wicofe-Installer deploys a validated Wicofe image and configures the documented boot handoff. OSynit (Open System Initialize) is the installed system's PID 1 and owns early userspace initialization, service startup, dependency ordering, failure recovery, and shutdown.

The installer must not reimplement OSynit service orchestration or start installed-system daemons as a substitute for first boot.

## 2. Deployment requirements

Before installation is finalized, validate that the selected image manifest:
- identifies the OSynit executable artifact and expected hash;
- contains the configured init path and compatible kernel handoff profile;
- contains the selected `/etc/osynit/` configuration and required unit/target files;
- declares the platform capabilities and architecture required by the selected OSynit build;
- does not require optional OSynit modules that are absent from the image.

The proposed executable path is `/usr/sbin/osynit`; the final path follows the accepted root filesystem layout.

## 3. First-boot ownership

Installer-owned tasks are disk deployment, boot-entry setup, and writing installation-specific configuration. OSynit-owned tasks begin after the kernel launches it as PID 1.

Initial machine identity, user/session setup, network configuration, device policy, time synchronization, audio, and desktop setup must be assigned to dedicated first-boot services or subsystem components. Do not place these domain implementations in PID 1.

## 4. Recovery and failure

If the init executable is missing or fails validation, the installer must not report a boot-ready installation. If first boot fails after deployment, preserve diagnostics and direct recovery to the Wicofe recovery environment; do not automatically repeat destructive installation operations.

## 5. Verification

Test the image-to-installer handoff, exact init path, architecture/ABI compatibility, first PID 1 launch, default target activation, failed-service recovery, and clean shutdown. Keep deployment success, emulator boot success, and physical hardware validation as separate evidence levels.
