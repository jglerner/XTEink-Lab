# XTEink Lab

> Engineering Notes for the XTEink Family

## Overview

**XTEink Lab** is a technical repository dedicated to documenting the installation, configuration, maintenance, and troubleshooting of XTEink e-readers under Linux.

The project is based on **real hardware**, **verified procedures**, and **reproducible testing**. Rather than being a collection of personal notes, its goal is to become a reliable technical reference for both XTEink users and Linux enthusiasts.

Current documentation focuses on the XTEink X4 running the CrossPoint firmware.

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
- CrossPoint Firmware

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
│   ├── XT-001-Initial-Setup-and-USB-Investigation.md
│   ├── XT-002-CrossPoint-Migration-Guide.md
│   ├── XT-003-USB-Architecture-of-the-XTEink-X4.md
│   ├── XT-004-esptool-Reference.md
│   ├── XT-005-Calibre-Integration.md
│   └── XT-006-FAQ.md

```

----

## Documentation Series

The documentation is organized into two complementary categories:

### Procedures

Step-by-step guides for installation, configuration, and maintenance.

- XT-001 – Initial Setup and USB Investigation
- XT-002 – CrossPoint Migration Guide

### Technical Notes

Engineering references describing the internal architecture and operation of the platform.

- XT-003 – USB Architecture of the XTEink X4

---

## Reference Platform

XTEink-Lab uses **Debian 13 (Trixie)** as its reference operating system.

The goal of this project is not to recommend a particular Linux distribution, but to provide a stable and reproducible engineering environment. All documented procedures are developed, tested, and validated on this platform.

Engineering documentation benefits from consistency. By using a mature and well-supported operating system as the reference platform, the procedures in this repository remain repeatable and easier to maintain over time.

---

