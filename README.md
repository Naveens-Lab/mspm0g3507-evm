# MSPM0G3507 Dev Board — EVM-Style Hardware Platform

> *An embedded software engineer's journey into hardware design.*

![Status](https://img.shields.io/badge/Status-REV%20A%20%E2%80%94%20Work%20In%20Progress-yellow)
![KiCad](https://img.shields.io/badge/KiCad-9.0-blue)
![MCU](https://img.shields.io/badge/MCU-TI%20MSPM0G3507SPTR-red)
![Security](https://img.shields.io/badge/Secure%20Element-ATECC608A-green)
![License HW](https://img.shields.io/badge/HW%20License-CERN--OHL--P-lightgrey)

---

## Overview

A custom EVM (Evaluation Module) style development board built around the **TI MSPM0G3507** microcontroller. The project started as a personal deep-dive into schematic and PCB design — something I had always wanted to do coming from an embedded software background — and has grown into a platform I intend to keep expanding.

The board includes a **Microchip ATECC608A** hardware secure element, making it suitable for exploring hardware-backed cryptography, device authentication, and secure key storage on a board I designed myself. Beyond the learning aspect, I plan to build real applications here: IoT device authentication, secure bootloader prototyping, and more.

---

## Board Preview

| Schematic | PCB (3D) |
|---|---|
| *(see `/kicad` folder)* | REV A — Naveen's LAB |

---

## Hardware — Bill of Materials

| Ref | Part | Description |
|---|---|---|
| U2 | **TI MSPM0G3507SPTR** | 32-bit Arm Cortex-M0+, 80MHz, 128KB Flash, LQFP-64 |
| U3 | **Microchip ATECC608A-SSHDA** | Hardware secure element — ECC, SHA-256, I2C interface |
| U1 | **SPX3819M5-L-3-3** | 3.3V LDO voltage regulator |
| U4 | **USBLC6-2SC6** | USB line ESD protection (bidirectional TVS) |
| U5 | **CH340E** | USB-to-UART bridge IC |
| J1 | **USB-C Receptacle** | USB 2.0, 16P — power input + UART comms |
| J2 | **TC2030 (Tag-Connect)** | SWD debug/programming header — no connector bulk |
| X1 | **8 MHz Crystal** | High-frequency external oscillator (HFXT) |
| R2, R3 | 5.1kΩ | I2C pull-ups for ATECC608A (SCL / SDA) |
| R5, R6 | 5.1kΩ | USB-C CC1 / CC2 pull-downs (UFP/sink mode) |

---

## Connectivity 

```
                    +3V3
                     │
  USB-C ─► USBLC6 ─► CH340E ──(UART0: PA0/PA1)──► MSPM0G3507SPTR
                              │                         │
                       UD+/UD-│                    I2C0 (PA10/PA11)
                              │                         │
                         (USB FS)                  ATECC608A-SSHDA
                                                   (Secure Element)

  SWD ─► TC2030 ──(SWDIO: PA19 / SWCLK: PA20)──► MSPM0G3507SPTR

  VBUS ─► SPX3819M5-L-3-3 (LDO) ─► +3V3 (board rail)
```

---

## Design Goals

- **EVM-style** — clean layout, labeled, easy to probe; breakout headers for GPIO access
- **Minimal but complete** — everything needed to get code running, nothing unnecessary
- **Secure element first-class** — ATECC608A on dedicated I2C0 bus with proper pull-ups and decoupling
- **Debug-friendly** — TC2030 Tag-Connect for SWD; no bulky 10-pin IDC connector needed
- **USB-C native** — power and UART over a single cable; USBLC6 ESD protection on data lines
- **Expandable** — designed with future revisions in mind; more peripherals, shields, and features planned

---

## Planned Features & Roadmap

- [x] MCU schematic — MSPM0G3507SPTR (LQFP-64)
- [x] ATECC608A-SSHDA secure element circuit (I2C0)
- [x] USB-C + USBLC6 ESD + CH340E UART bridge
- [x] SPX3819M5-L-3-3 3.3V LDO power circuit
- [x] TC2030 SWD debug header
- [x] 8 MHz HFXT crystal circuit
- [x] PCB layout — REV A (in progress, pre-fab)
- [x ] DRC clean pass + design review
- [ ] Board bring-up firmware (MSPM0 DriverLib, bare-metal)
- [ ] ATECC608A I2C driver + device wake-up sequence
- [ ] ATECC608A provisioning (TrustFLEX / TrustCustom configuration)
- [ ] Hardware-backed device authentication demo (ECC508 challenge-response)
- [ ] Secure bootloader prototype using ATECC608A key storage
- [ ] USB CDC firmware (v2 — remove CH340E dependency)
- [ ] QSPI external flash (future revision)
- [ ] OLED / e-ink display header (future revision)

---

## Tools

- **KiCad 9.0** — Schematic capture and PCB layout
- **TI MSPM0G3507 Datasheet + TRM** — MCU peripheral and pin configuration reference
- **TI MSPM0 SDK / DriverLib** — Firmware development
- **Microchip ATECC608A Datasheet + CryptoAuthLib** — Secure element integration reference

---

## Repository Structure

```
mspm0g3507-evm/
├── kicad/
│   ├── MSPM0_DevBoard.kicad_pro
│   ├── MSPM0_DevBoard.kicad_sch
│   ├── MSPM0_DevBoard.kicad_pcb
│   └── MSPM0G3507_Sym_Symbl.kicad_sym     # Custom MCU symbol library
├── datasheets/                              # MSPM0G3507, ATECC608A, CH340E, SPX3819
├── gerbers/                                 # Generated at fab-ready milestone
├── firmware/                                # Bring-up and demo firmware
└── README.md
```

---

## Why This Project?

I have spent 6+ years as an embedded software engineer  writing firmware for automotive , elevator controllers, medical devices, and defense systems. I have always worked *on top of* hardware that someone else designed. This project is me crossing that line: learning how the schematic becomes a PCB, how a PCB becomes a product, and how every decision made during layout shows up as a bug (or not) at bring-up.

The ATECC608A is here because hardware-backed security is something I want to understand deeply  not just at the register level from firmware, but from the moment the footprint lands on the board.

This is REV A. It will not be perfect. That is the point.

---

## License

Hardware design files (schematic, PCB) licensed under [CERN Open Hardware Licence v2 - Permissive (CERN-OHL-P)](https://ohwr.org/cern_ohl_p_v2.txt).  
Firmware (when added) licensed under MIT.

---

## Author

**Naveen** — Embedded Systems Engineer  
Firmware architecture · ISO 26262 ASIL-D · Hardware-firmware integration  
Automotive · Industrial · Defense

[![GitHub](https://img.shields.io/badge/GitHub-Naveens--Lab-black?logo=github)](https://github.com/Naveens-Lab)
