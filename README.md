# Dual-Band Solar LoRa Mesh

Lora-Bridge: Unified Dual-Band (433 and 868 MHz) Solar Node for Meshtastic / Meshcore

[![License: CERN OHL-S v2](https://img.shields.io/badge/License-CERN%20OHL--S%20v2-blue.svg)](https://ohwr.org/cern_ohl_s_v2.txt)
[![Meshtastic](https://img.shields.io/badge/Meshtastic-Compatible-green.svg)](https://meshtastic.org)
[![Meshcore](https://img.shields.io/badge/Meshcore-Compatible-green.svg)](https://meshcore.io)
[![Hardware](https://img.shields.io/badge/Hardware-Open%20Source-orange.svg)](#)

**Lora-Bridge** is an open-source, ultra-rugged, solar-powered dual-band Meshtastic relay node. It merges the elegant mechanical and power architecture of the **MASN** (Meshtastic Autonomous Solar Node) with a heavy-duty, industrial **Faketec-inspired** design and a hardware-integrated dual-band cross-frequency bridge system. 

Unlike standard single-frequency nodes, this design bridges the gap between Portugal's distinct regional Meshtastic networks by establishing a local hardware **UART bridge** between two dedicated **Heltec HT-RA62** LoRa modules: one operating on **433 MHz (LF)** and the other on **868 MHz (HF)**.

---

## Key Features

- **Faketec-Style Mechanical Design:** Extremely ruggedized, impact-resistant, and weather-sealed layout featuring a specialized CNC/3D-printed internal chassis, robust heavy-duty latches, and high-clearance gland placement.
- **Dual HT-RA62 Modules:** Optimized exclusively around two identical Heltec HT-RA62 Semtech SX1262-based modules (1x Low-Frequency 433 MHz and 1x High-Frequency 868 MHz), ensuring balanced power consumption and identical RF driver logic.
- **Cross-Frequency UART Bridge:** Hardware serial interconnect loop acting as an autonomous cross-band repeater. Packets arriving on 433 MHz are parsed and repeated immediately over 868 MHz, and vice versa.
- **MASN Solar Core Power Path:** Complete MPPT solar charging circuit optimized for high efficiency during low-light winter days, integrated with comprehensive over-current, over-charge, and temperature protections for a 18650/21700 Li-ion battery bank.
- **Zero RF Internal Interference:** Dedicated isolated internal shielding compartments preventing cross-harmonic desensitization between the co-located 433 MHz and 868 MHz antennas.

---

## System Architecture

### Hardware Block Diagram

[ 12V / 5V Solar Panel ]
                 │
                 ▼
       [ MASN MPPT Solar Charger ]
                 │
                 ├──────────────┐
                 ▼              ▼
       [ Protection Circuit ] [ Battery Bank (21700) ]
                 │
     ┌───────────┴───────────┐
     │ (3.3V Pure Rail)       │ (3.3V Pure Rail)
     ▼                       ▼

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

# For Module 1 (433 MHz)
meshtastic --set serial.enabled true --set serial.baud B115200 --set serial.mode TEXTMSG

# For Module 2 (868 MHz)
meshtastic --set serial.enabled true --set serial.baud B115200 --set serial.mode TEXTMSG

Assembly & Mechanical Build
3D Printing the Frame: Download the structural STL files located in the /hardware/enclosure directory. Print the internal frame plate using PETG or ASA to withstand high internal temperatures during intense summer conditions.

PCB Population: Solder the passive solar components onto the main board following the schematic provided in /hardware/pcb. Mount the two HT-RA62 modules into their respective isolated slots.

RF Separation Setup: Ensure that the 433 MHz and 868 MHz external antennas are physically spaced apart at least 50 cm or oriented perpendicular to each other on the external mounting bracket to limit localized desensitization.

Repository Structure
├── firmware/              # Bridge communication configs and node flash binaries
├── hardware/
│   ├── pcb/               # KiCad schematics, layout design, and Gerber files
│   └── enclosure/         # Faketec-style industrial 3D prints and step files
├── docs/                  # Detailed assembly documentation and RF guidelines
└── README.md              # Project overview
Contributing
Contributions are heavily welcomed! If you are optimizing the RF trace filters for the HT-RA62 or improving the serial bridging packet structure, please feel free to open an Issue or submit a Pull Request.

License
This project is licensed under the CERN Open Hardware Licence Version 2 – Strongly Reciprocal (CERN OHL-S v2). You are free to copy, modify and distribute this design, provided you maintain identical licensing protections on any derivative works. See the LICENSE file for full conditions.

Acknowledgments
Inspired heavily by Daniel P. Costas' MASN Node.

Bridging architecture concept derived from Meshcore Portugal (WSL3 Tutorials).

Mechanical aesthetics adapted from industrial Faketec field node enclosures.
