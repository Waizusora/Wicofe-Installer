# Frenit First-Boot Integration 0.1

- **Status:** Proposed installation integration; native Frenit first boot is not implemented or verified.
- **Owner:** Wicofe-Installer for deployment; Frenit for init and service lifecycle policy.
- **Component:** [Frenit — Free Init](https://github.com/Waizusora/Frenit).
- **Cross-component contract:** [WIC-SPEC-BOOT-FRENIT-001](https://github.com/Waizusora/Wicofe-Spec/blob/main/boot/FRENIT_INTEGRATION_CONTRACT_0.1.md).
- **Date:** 2026-11-18

## 1. Responsibility boundary

Wicofe-Installer deploys a validated Wicofe image and configures only the boot behavior described by accepted kernel/image specifications. Frenit is the canonical init-system project and will own early userspace service lifecycle after the kernel launches it as PID 1.

The installer must not reimplement Frenit service orchestration, invent an init path, or start installed-system daemons as a substitute for a verified first boot.

## 2. Deployment requirements

Before installation is reported boot-ready, validate that the selected image manifest:

- identifies the Frenit artifact revision and expected hash;
- contains an architecture- and ABI-compatible executable and its runtime dependencies;
- contains configuration/service metadata matching the Frenit schema version;
- declares the required kernel capabilities and supported boot handoff profile;
- does not require optional capabilities absent from the image or kernel.

Do not hardcode a proposed Frenit executable path or configuration directory before the root-filesystem layout and Frenit schema are accepted.

## 3. First-boot ownership

Installer-owned tasks are disk deployment, boot-entry setup, and writing installation-specific configuration. Frenit-owned tasks begin only after the kernel has launched it through the verified initial-userspace handoff.

Machine identity, user/session setup, network configuration, device policy, time synchronization, audio and desktop setup must be assigned to dedicated first-boot services or subsystem components, not placed inside PID 1.

## 4. Recovery and failure

If the init executable is missing or fails validation, the installer must not report a boot-ready installation. If first boot fails after deployment, preserve diagnostics and direct recovery to Wicofe-Recovery; do not automatically repeat destructive installation operations.

## 5. Verification

Test image-to-installer handoff, artifact hash and architecture, ABI compatibility, first PID 1 launch, default target activation, failed-service recovery and clean shutdown. Keep deployment success, emulator boot success and physical-hardware validation as separate evidence levels.

No native Frenit first-boot claim is allowed until the kernel/ABI and Wicofe-Test gates pass.
