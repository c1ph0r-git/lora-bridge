# Dual-Band Solar LoRa Mesh

Lora-Bridge: Unified Dual-Band (433 and 868 MHz) Solar Node for Meshtastic / Meshcore

[![License: CERN OHL-S v2](https://img.shields.io/badge/License-CERN%20OHL--S%20v2-blue.svg)](https://ohwr.org/cern_ohl_s_v2.txt)
[![Meshtastic](https://img.shields.io/badge/Meshtastic-Compatible-green.svg)](https://meshtastic.org)
[![Meshcore](https://img.shields.io/badge/Meshcore-Compatible-green.svg)](https://meshcore.io)
[![Hardware](https://img.shields.io/badge/Hardware-Open%20Source-orange.svg)](#)

**Lora-Bridge** is an open-source, community-driven, solar-powered dual-band LoRa relay node. It merges the elegant architecture design of the **MASN** (Meshtastic Autonomous Solar Node) with a **Faketec-inspired** design and a hardware-integrated dual-band cross-frequency bridge system. 

Unlike standard single-frequency nodes, this design bridges the gap between distinct regional Meshtastic/Meshcore networks by establishing a local hardware **UART bridge** between two dedicated **LoRa** modules: one operating on **433 MHz (LF)** and the other on **868 MHz (HF)**.

Using this repeater bridge, client nodes can use either frequency and talk to others in a different frequency without requiring a second node, which improves the scope of communication as well as the resilience of the network.

---

## Key Features

- **Dual LoRa Modules:** Optimized exclusively around two identical Heltec HT-RA62 Semtech SX1262-based modules (1x Low-Frequency 433 MHz and 1x High-Frequency 868 MHz), ensuring balanced power consumption and identical RF driver logic.
- **Cross-Frequency UART Bridge:** Hardware serial interconnect loop acting as an autonomous cross-band repeater. Packets arriving on 433 MHz are parsed and repeated immediately over 868 MHz, and vice versa.
- **Solar Core Power Path:** Complete MPPT solar charging circuit optimized for high efficiency during low-light winter days, integrated with comprehensive over-current and over-charge protections for Lithium batteries.
- **Low Power Consumption:** The nRF52840 MCU of the promicro / nice!nano boards has a very low power consumption, ideal for a solar design.
- **Sensor Integration and Telemetry:** This design integrates voltage/current sensor for the solar panel, battery and load, as well as enviromental sensor for temperature and humidity. 
- **Faketec-Style Mechanical Design:** Extremely ruggedized layout featuring a specialized chassis and high-clearance placement. Easy to DIY.
- **Minimal RF Internal Interference:** Dedicated isolated internal paths and shielding preventing cross-harmonic desensitization between the co-located 433 MHz and 868 MHz tracks.
- **Modular Design:** this design choice makes it great to test different components in a clean framework.
- **Price:** this PCB is based around easily available and cheap modules.

---

## Motivation and Reasoning

Individual modules are cheap but breadboard-based builds often end up with a jungle of wires, unreliable connections, and an overall setup that easily leads to mistakes.

Anyone approaching LoRa mesh networks usually finds the same thing: tutorials full of tangled wires, tiny solder joints, and boards designed for people with solid electronics experience. That’s discouraging for anyone just getting started — and even more frustrating if your goal is to build a stable solar-powered node for the roof or the field.

This PCB solves this by integrating everything into a single, compact design.

A PCB anyone can assemble in a short amount of time, without microscopes nor messy wiring. The idea isn’t just to make it work, but to help you learn through the process. Building your own node gives you a deeper understanding of the Meshtastic / Meshcore ecosystem and lets you get the most out of it.

### Key Advantages
- Uses only THT components, easy to solder.
- Integrates all connections into the board — no messy wiring.
- Accepts standard modules that plug in directly.
- Includes an MPPT solar charger and sensor telemetry features.
- Simplifies maintenance and testing: swap a module without rebuilding the whole node.

---

## PCB Architecture

### Hardware Block Diagram

>[ 12V / 5V Solar Panel ]
>                 │
>                 ▼
>       [ MASN MPPT Solar Charger ]
>                 │
>                 ├──────────────┐
>                 ▼              ▼
>       [ Protection Circuit ] [ Battery Bank (21700) ]
>                 │
>     ┌───────────┴───────────┐
>     │ (3.3V Pure Rail)       │ (3.3V Pure Rail)
>     ▼                       ▼

---

## Hardware Specifications

| Component | Specification | Description |
| :--- | :--- | :--- |
| **LoRa Transceiver (LF)** | Heltec HT-RA62 (433 MHz) | Handles local Portuguese standard sub-giga mesh |
| **LoRa Transceiver (HF)** | Heltec HT-RA62 (868 MHz) | Handles standard European European backbone mesh |
| **Bridge Interface** | Direct Hardware UART | Interspersed RX/TX cross-over at `115200 bps` |
| **Solar Controller** | MASN Integrated MPPT | High-efficiency step-down solar tracker |
| **Battery Chemistry** | Lithium-Ion / LiFePO4 | Configurable cell holders optimized for 21700 cells |
| **Enclosure Rating** | IP67 Equivalent | Faketec-inspired thick-walled polycarbonate structure |

---

## Interconnection & Bridge Configuration

The Portuguese cross-frequency bridging architecture utilizes a bidirectional Serial/UART passthrough mechanism. Packets originating from the low-frequency 433 MHz grid are received by the primary HT-RA62, transmitted out its hardware TX pin, and injected natively into the secondary HT-RA62's RX pin to be broadcasted instantly to the 868 MHz network.

### UART Pinout Interconnect
To set up the physical bridge between the two Heltec HT-RA62 modules, route the cross-over communication as follows:

| Module 1 (433 MHz LF) | Module 2 (868 MHz HF) | Description |
| :--- | :--- | :--- |
| `TX` (GPIO X) | `RX` (GPIO Y) | 433 MHz Traffic to 868 MHz Grid |
| `RX` (GPIO Y) | `TX` (GPIO X) | 868 MHz Traffic to 433 MHz Grid |
| `GND` | `GND` | Common Ground Reference |

### Meshtastic Firmware Settings
Both modules must be configured using the Meshtastic CLI or App to allow serial module pass-through framing:

- For Module 1 (433 MHz)
meshtastic --set serial.enabled true --set serial.baud B115200 --set serial.mode TEXTMSG

- For Module 2 (868 MHz)
meshtastic --set serial.enabled true --set serial.baud B115200 --set serial.mode TEXTMSG

## Assembly & Mechanical Build
3D Printing the Frame: Download the structural STL files located in the /hardware/enclosure directory. Print the internal frame plate using PETG or ASA to withstand high internal temperatures during intense summer conditions.

PCB Population: Solder the passive solar components onto the main board following the schematic provided in /hardware/pcb. Mount the two HT-RA62 modules into their respective isolated slots.

RF Separation Setup: Ensure that the 433 MHz and 868 MHz external antennas are physically spaced apart at least 50 cm or oriented perpendicular to each other on the external mounting bracket to limit localized desensitization.

## Repository Structure
├── firmware/              # Bridge communication configs and node flash binaries
├── hardware/
│   ├── pcb/               # KiCad schematics, layout design, and Gerber files
│   └── enclosure/         # Faketec-style industrial 3D prints and step files
├── docs/                  # Detailed assembly documentation and RF guidelines
└── README.md              # Project overview

## Contributing
Contributions are heavily welcomed! If you are optimizing the RF trace filters for the HT-RA62 or improving the serial bridging packet structure, please feel free to open an Issue or submit a Pull Request.

## License
This project is licensed under the CERN Open Hardware Licence Version 2 – Strongly Reciprocal (CERN OHL-S v2). You are free to copy, modify and distribute this design, provided you maintain identical licensing protections on any derivative works. See the LICENSE file for full conditions.

## Acknowledgments
Heavily inspired by Daniel P. Costas' MASN Node.

Bridging architecture concept derived from Meshcore Portugal (WSL3 Tutorials).

Mechanical aesthetics adapted from Faketec node design.
