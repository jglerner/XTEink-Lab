# XT-002 — CrossPoint 1.4.1 Firmware Upgrade Guide

**Document ID:** XT-002  
**Applies to:** XTEink X4 Reader  
**Firmware Version:** CrossPoint 1.4.1  
**Host Operating System:** Debian 13 (Trixie)  
**Author:** Jacques Lerner  
**Document Status:** Draft

---

## 1. Purpose

This document provides a **verified engineering procedure** for upgrading the **XTEink X4 Reader** to **CrossPoint Firmware Version 1.4.1** using a Debian 13 (Trixie) Linux workstation.

This guide describes the complete firmware upgrade process, including:

- Verifying USB connectivity.
- Verifying communication with the onboard ESP32-C3 microcontroller.
- Backing up the existing firmware (optional but recommended).
- Installing CrossPoint Firmware Version 1.4.1.
- Verifying a successful installation.

CrossPoint Firmware **Version 1.4.1** represents an important milestone for the XTEink X4. Once CrossPoint Firmware Version 1.4.1 has been successfully installed, subsequent firmware upgrades can normally be performed using the official **CrossPoint Flash Tools**, eliminating the need to repeat the complete command-line flashing procedure described in this guide.

The official CrossPoint Flash Tools are available at:

<https://crosspointreader.com/#flash-tools>

The objective of this document is to provide a **repeatable**, **reliable**, and **fully documented** upgrade procedure that reduces the risk of communication failures or incorrect firmware installation.

> **Document Philosophy**
>
> This guide documents a **verified engineering procedure** rather than a tutorial or a record of experimentation. Every command and verification step has been validated on the reference platform to provide a reliable and repeatable process.

All commands, expected results, and verification steps presented in this document were validated during the development of the **XTEink-Lab** project and are intended to serve as a practical engineering reference for future firmware maintenance.

---

## 2. Scope

This document applies exclusively to the **XTEink X4 Reader** being upgraded to **CrossPoint Firmware Version 1.4.1** from a Linux workstation running **Debian 13 (Trixie)**.

The scope of this guide includes:

- Verifying USB connectivity between the host computer and the XTEink X4.
- Verifying communication with the onboard ESP32-C3 microcontroller.
- Creating an optional backup of the existing firmware.
- Flashing CrossPoint Firmware Version 1.4.1.
- Verifying that the firmware upgrade completed successfully.
- Providing troubleshooting guidance for common issues encountered during the upgrade process.

This document does **not** cover:

- Installation or configuration of Debian Linux.
- General ESP32 development or programming.
- Building CrossPoint Firmware from source.
- Hardware modifications or repair of the XTEink X4.
- Firmware versions other than CrossPoint Version 1.4.1, except where explicitly noted.

## 3. Requirements

Before beginning the firmware upgrade procedure, verify that the following requirements are met.

### Hardware

- XTEink X4 Reader.
- USB-C data cable capable of both power and data transfer.
- Linux workstation running Debian 13 (Trixie).
- A USB port with reliable power and data connectivity.

### Software

The following software packages must be installed on the host system:

- Python 3
- `pip`
- `esptool.py` (or the current `esptool` package)
- Git (optional, if obtaining firmware or documentation from a repository)

### Firmware

The following files must be available before starting the procedure:

- CrossPoint Firmware Version 1.4.1 image.
- Any additional firmware components required by the release.

### User Requirements

The user should be familiar with:

- Opening a Linux terminal.
- Executing command-line programs.
- Navigating the Linux file system.
- Using `sudo` when administrative privileges are required.

### Recommendations

Although not mandatory, the following are strongly recommended:

- Ensure the XTEink X4 battery is adequately charged before beginning the upgrade.
- Close applications that may interfere with USB device access.
- Perform the upgrade on a stable system without interrupting power to either the computer or the XTEink X4.
- Create a backup of the existing firmware before flashing, whenever possible.

## 4. Verify USB Connection

Before attempting to communicate with the XTEink X4 or flashing new firmware, verify that the device is correctly detected by the host operating system.

### 4.1 Connect the Device

1. Power off the XTEink X4.
2. Connect the reader to the Debian workstation using a USB-C cable capable of data transfer.
3. Connect the cable directly to a USB port on the computer whenever possible, avoiding USB hubs during the upgrade process.

### 4.2 Verify USB Detection

Verifying USB connectivity is the first diagnostic step in the firmware upgrade process. Before attempting to communicate with the ESP32-C3 or flashing new firmware, confirm that the Linux kernel detects the XTEink X4 as a USB device.

Failure to detect the device at this stage is typically caused by a USB cable that does not support data transfer, an unsuitable USB port, or an incorrect device state. These issues should be resolved before proceeding to the next section.

Open a terminal and execute:

```bash
lsusb
```

The XTEink X4 should appear in the list of detected USB devices.

If the device is not listed:

- Verify that the USB cable supports data transfer.
- Try a different USB port.
- Reconnect the cable.
- Confirm that the reader is powered on if required by the firmware mode being used.

### 4.3 Monitor USB Events (Optional)

If additional verification is required, monitor the kernel messages while connecting the device:

```bash
dmesg --follow
```

or

```bash
journalctl -kf
```

Connect the XTEink X4 and verify that the Linux kernel reports the detection of a new USB device.

Successful USB detection confirms that communication between the workstation and the XTEink X4 can proceed.

> **Note**
>
> Many USB-C cables supplied with chargers are intended only for power delivery and do not carry USB data. If the XTEink X4 is not detected, verify that the cable supports both power and data transfer before investigating other causes.

## 5. Verify Communication with the ESP32-C3

Once the Linux operating system has successfully detected the XTEink X4 as a USB device, verify that communication with the onboard ESP32-C3 microcontroller is working correctly.

This step confirms that the host computer can exchange data with the ESP32-C3 bootloader before attempting to read or write the device's flash memory.

### 5.1 Identify the Serial Device

Verify that Linux has created a serial device for the XTEink X4:

```bash
ls /dev/ttyACM*
```

Typical output:

```text
/dev/ttyACM0
```

If no device is found:

- Disconnect and reconnect the XTEink X4.
- Verify the USB cable supports data transfer.
- Repeat the USB verification procedure described in Section 4.

### 5.2 Verify Communication

Execute:

```bash
esptool.py --port /dev/ttyACM0 chip_id
```

A successful response identifies the ESP32-C3 and confirms that communication with the bootloader has been established.

If communication fails, verify that:

- The correct serial device is being used.
- The reader is in the required firmware or bootloader mode.
- No other application is currently using the serial port.

Do not continue until communication with the ESP32-C3 has been successfully established.

## 6. (Optional) Backup Existing Firmware

Creating a backup of the existing firmware is recommended before flashing CrossPoint Firmware Version 1.4.1.

Although the official firmware images are publicly available, a backup preserves the exact contents of the device's flash memory at the time of the upgrade. This may be valuable for troubleshooting, comparing firmware revisions, or restoring the device to its previous state if required.

### 6.1 Create a Backup Directory

Create a directory to store the backup files:

```bash
mkdir -p ~/xteink-backup
cd ~/xteink-backup
```

### 6.2 Read the Flash Memory

Use `esptool.py` to read the complete flash memory:

```bash
esptool.py --port /dev/ttyACM0 read_flash 0x000000 0x400000 xteink_backup.bin
```

> **Note**
>
> The flash size shown above is an example. Verify the actual flash capacity of your XTEink X4 before performing the backup.

### 6.3 Verify the Backup

Confirm that the backup file was successfully created:

```bash
ls -lh xteink_backup.bin
```

Store the backup in a safe location before continuing with the firmware upgrade.

## 7. Install CrossPoint Firmware Version 1.4.1

After verifying USB connectivity, confirming communication with the ESP32-C3, and (optionally) creating a backup of the existing firmware, the XTEink X4 is ready for the initial installation of **CrossPoint Firmware Version 1.4.1**.

This procedure is generally required only once. After CrossPoint Firmware Version 1.4.1 has been successfully installed, future firmware updates can normally be performed using the official CrossPoint Flash Tools.

### 7.1 Download the Firmware

Download the official **CrossPoint Firmware Version 1.4.1** release from the CrossPoint project and save it to your local system.

### 7.2 Install the Firmware

Follow the installation procedure described by the CrossPoint project using the downloaded firmware image.

The installation process writes the CrossPoint firmware to the XTEink X4 and prepares the device for subsequent upgrades using the official CrossPoint update mechanism.

Do **not** disconnect the USB cable or interrupt power during the installation process.

### 7.3 Verify Successful Installation

When the installation has completed successfully, disconnect and restart the XTEink X4.

The device should boot normally and report **CrossPoint Firmware Version 1.4.1**.

If the installation does not complete successfully, refer to the **Troubleshooting** section before attempting the procedure again.

> **Important**
>
> CrossPoint Firmware Version 1.4.1 is the migration release that transitions the XTEink X4 to the CrossPoint firmware ecosystem. Once this version has been successfully installed, future firmware upgrades can normally be performed using the official CrossPoint Flash Tools without repeating the complete migration procedure documented in this guide.

## 8. Verify Installation

After installing CrossPoint Firmware Version 1.4.1, verify that the XTEink X4 starts correctly and is operating as expected.

### 8.1 Restart the Device

Disconnect the USB cable, if necessary, and restart the XTEink X4.

Allow the device to complete its normal boot sequence.

### 8.2 Verify Firmware Version

Open the device's **Settings** menu and confirm that the reported firmware version is:

```text
CrossPoint Firmware Version 1.4.1
```

This confirms that the installation completed successfully.

### 8.3 Verify Device Operation

Perform a brief functional check to ensure that the device is operating normally.

Recommended checks include:

- The device boots without errors.
- The display refreshes correctly.
- Menus respond as expected.
- USB communication is available when the device is connected to a computer.

### 8.4 Ready for Future Updates

The XTEink X4 has now been successfully migrated to the CrossPoint firmware ecosystem.

Future firmware releases can normally be installed using the official CrossPoint update procedure without repeating the migration process described in this document.

## 9. Troubleshooting

This section summarizes the most common issues encountered during the initial migration of an XTEink X4 to CrossPoint Firmware Version 1.4.1.

### USB Device Not Detected

**Symptoms**

- `lsusb` does not show the XTEink X4.
- No new USB device appears when the cable is connected.

**Possible Causes**

- USB cable supports charging only.
- Faulty USB cable.
- Faulty USB port.
- Device not properly connected.

**Recommended Actions**

- Verify that the USB-C cable supports data transfer.
- Try a different USB port.
- Reconnect the device.
- Monitor kernel messages using:

```bash
dmesg --follow
```

---

### Serial Device Not Available

**Symptoms**

- `/dev/ttyACM0` does not exist.
- `esptool.py` reports that the serial port cannot be opened.

**Possible Causes**

- USB connection not established.
- Device not in the required communication mode.
- Another application is using the serial port.

**Recommended Actions**

- Repeat the USB verification procedure.
- Disconnect and reconnect the XTEink X4.
- Close any application that may be accessing the serial port.
- Verify the correct serial device name.

---

### ESP32-C3 Communication Failed

**Symptoms**

- `esptool.py` cannot identify the ESP32-C3.
- Connection attempts time out.

**Possible Causes**

- Incorrect serial device.
- Device not in bootloader mode.
- USB communication problem.

**Recommended Actions**

- Verify the serial port.
- Repeat the communication test described in Section 5.
- Reconnect the device and try again.

---

### Firmware Installation Failed

**Symptoms**

- The flashing process terminates with an error.
- Verification fails.
- The installation does not complete.

**Recommended Actions**

- Do not disconnect the device until the process has completely stopped.
- Repeat the installation.
- Verify that the firmware image was downloaded correctly.
- Confirm that the correct firmware version is being used.

---

### Device Does Not Boot After Installation

**Symptoms**

- The XTEink X4 does not start normally after installation.

**Recommended Actions**

- Restart the device.
- Repeat the installation procedure if necessary.
- Restore the original firmware from the backup, if one was created.
- Consult the official CrossPoint project documentation for additional recovery procedures.

---

### Still Having Problems?

If the procedures described in this guide do not resolve the issue, consult the official CrossPoint project documentation and community resources before attempting further modifications to the device.

## 10. References

The following resources provide additional information related to the installation and operation of CrossPoint Firmware on the XTEink X4.

### Normative References

The following references are required or recommended for completing the procedures described in this document.

- CrossPoint Project Repository
- CrossPoint Firmware Releases
- CrossPoint Installation Instructions

### Informative References

The following references provide additional technical background.

- Espressif ESP32-C3 Documentation
- Espressif esptool Documentation
- XTEink-Lab Documentation Collection

---
