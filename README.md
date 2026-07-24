# XTEink Lab

> Engineering Notes for the XTEink Family

## Overview

**XTEink Lab** is a technical repository dedicated to documenting the installation, configuration, maintenance, and troubleshooting of XTEink e-readers under Linux.

The project is based on **real hardware**, **verified procedures**, and **reproducible testing**. Rather than being a collection of personal notes, its goal is to become a reliable technical reference for both XTEink users and Linux enthusiasts.

Current documentation focuses on the **XTEink X4** running **CrossPoint 1.4.1**.

---

## Philosophy

This repository follows a few simple principles:

- Test first, document second.
- Record verified facts rather than assumptions.
- Keep procedures reproducible.
- Document failures as carefully as successes.
- Improve documentation as new firmware versions become available.

---

## Current Test Environment

### Host Computer

- Debian GNU/Linux 13 (Trixie)
- GNOME Desktop
- USB-C connectivity
- ESP32 development tools
- Calibre

### Device

- XTEink X4
- CrossPoint 1.4.1

Future support for additional XTEink models (such as the X3) will be added as they are independently tested and documented.

---

## Repository Structure

```
xteink-lab/
│
├── README.md
├── LICENSE
├── .gitignore
│
├── docs/
│   ├── XT-001-X4-Recovery.md
│   ├── XT-002-CrossPoint-1.4.1-Upgrade.md
│   ├── XT-003-USB-Architecture.md
│   ├── XT-004-esptool-Reference.md
│   ├── XT-005-Calibre-Integration.md
│   └── XT-006-FAQ.md
