# XT-001 — Initial Setup and USB Investigation

**Document ID:** XT-001  
**Status:** Verified  
**Applies to:** XTEink X4  
**Firmware:** CrossPoint 1.2.0 → 1.4.1  
**Last Updated:** July 2026

---

**Applies to:**

- XTEink X4
- CrossPoint 1.2.0 → 1.4.1

---

# Objective

Document the investigation of the initial USB communication between a Debian GNU/Linux workstation and the XTEink X4, culminating in a successful firmware upgrade to CrossPoint 1.4.1.

This document establishes the baseline configuration for future work and records the verified behavior of the device under Linux.

---

## Summary

| Item | Result |
|------|--------|
| USB Communication | Verified |
| ESP32 Detection | Verified |
| Serial Interface | Verified |
| Firmware Upgrade | Successful |
| USB Mass Storage | Not Supported by CrossPoint |

---

# Hardware

## Host Computer

- **Operating System:** Debian GNU/Linux 13 (Trixie)
- **Desktop Environment:** GNOME
- **USB Interface:** USB-C

## Device

- **Model:** XTEink X4
- **Processor:** ESP32-C3
- **Initial Firmware:** CrossPoint 1.2.0
- **Final Firmware:** CrossPoint 1.4.1

---

# Initial Symptoms

After connecting the XTEink X4 to the computer:

- The battery charged normally.
- No removable storage appeared in Nautilus.
- No USB storage device appeared in `lsblk`.
- The expected user experience was similar to connecting a USB flash drive.

At this stage, it was unclear whether the problem originated from:

- Linux
- the USB cable
- the XTEink hardware
- the CrossPoint firmware

---

# Investigation

The investigation followed a structured elimination process.

## Step 1 — USB Detection

Linux detected an Espressif USB Serial/JTAG device.

This confirmed that the computer could communicate with the XTEink at the USB level.

---

## Step 2 — Serial Communication

Communication through `/dev/ttyACM0` was verified using the ESP32 toolchain.

The following information was successfully retrieved:

- Chip identification
- MAC address
- Flash identification
- eFuse configuration

These results demonstrated that the USB communication path was fully operational.

---

## Step 3 — User Permissions

The Linux user was added to the `dialout` group to obtain access to the serial interface.

Group membership was verified after starting a new login session.

---

## Step 4 — ESP32 Toolchain

Two different versions of `esptool` were discovered:

- Debian package
- `pipx` installation

The `pipx` installation was selected as the preferred version to avoid command inconsistencies.

---

## Step 5 — USB Storage Investigation

Several Linux utilities were used to determine whether the X4 exported a storage device.

The following commands were examined:

- `lsusb`
- `lsblk`
- `gio mount -li`

Results showed:

- USB Serial interface present.
- No USB Mass Storage device.
- No MTP device.

---

# Root Cause Analysis

The investigation identified multiple independent factors.

## Confirmed

- The original USB cable was unsuitable for reliable data communication.
- Linux serial permissions required membership in the `dialout` group.
- Two versions of `esptool` created unnecessary confusion.

## Verified Device Behavior

CrossPoint exposes an ESP32 USB Serial/JTAG interface for communication and firmware management.

The firmware does **not** present the XTEink X4 as a USB Mass Storage device.

This behavior is consistent with the observed Linux device enumeration.

---

# Firmware Upgrade

Once reliable USB communication had been verified, the device was upgraded using the official CrossPoint Web Installer.

| Previous Version | New Version |
|------------------|-------------|
| CrossPoint 1.2.0 | CrossPoint 1.4.1 |

The upgrade completed successfully without data loss or unexpected behavior.

---

# Verification

Following the upgrade:

- Device booted normally.
- CrossPoint 1.4.1 reported correctly.
- USB serial communication remained operational.
- Firmware flashing capability was confirmed.

No abnormal behavior was observed during post-upgrade testing.

---

# Lessons Learned

- Verify the USB cable before investigating software.
- Confirm Linux device detection before assuming firmware problems.
- Distinguish USB Serial from USB Mass Storage.
- Eliminate one hypothesis at a time.
- Document verified observations instead of assumptions.

---

# Conclusion

The investigation demonstrated that the XTEink X4 operated correctly throughout the process.

The observed issues were related to the host environment and to assumptions about the device's USB behavior rather than to any hardware fault.

The successful firmware upgrade established a verified baseline configuration for all subsequent documentation in this repository.

---

## Assumed Knowledge

XTEink-Lab is intended for users who are comfortable working in a Linux environment and performing basic system administration tasks.

The procedures documented in this repository assume that the reader is familiar with:

- Using a Linux terminal.
- Navigating the file system.
- Executing command-line programs.
- Editing text files with a preferred editor.
- Using `sudo` when administrative privileges are required.

The objective of this repository is to document verified engineering procedures rather than provide introductory Linux tutorials. Readers who require guidance on fundamental Linux concepts should consult the appropriate documentation before following the procedures described here.

---

# References

- XT-002 — CrossPoint 1.4.1 Upgrade *(planned)*
- Official CrossPoint documentation
- ESP32 `esptool`
