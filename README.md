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

### Schematic Diagram



#### Interconnection & Bridge Configuration

The Portuguese cross-frequency bridging architecture utilizes a bidirectional Serial/UART passthrough mechanism. Packets originating from the low-frequency 433 MHz grid are received by the primary HT-RA62, transmitted out its hardware TX pin, and injected natively into the secondary HT-RA62's RX pin to be broadcasted instantly to the 868 MHz network.

##### UART Pinout Interconnect
To set up the physical UART-1 bridge between the two nRf52840 modules (promicro or nice!nano), route the cross-over communication as follows:

| Module 1 (433 MHz LF) | Module 2 (868 MHz HF) | Description |
| :--- | :--- | :--- |
| `TX` (Pin 3: P0.08) | `RX` (Pin 2: P0.06) | 433 MHz Traffic to 868 MHz Grid |
| `RX` (Pin 2: P0.06) | `TX` (Pin 3: P0.08) | 868 MHz Traffic to 433 MHz Grid |
| `GND` | `GND` | Common Ground Reference |

### PCB Design a Layout

![pcb](images/pcb.png)

![pcb](images/pcb-2.png)

### Dimensions and Mounting
PCB Size: 63.4 mm × 96.9 mm
Mounting Holes: 56.7 mm × 90 mm

### Ordering the PCB - Gerber File

You can order the PCB directly from JLCPCB or PCBWay (the design files are linked in the Bill of Materials).

The process is very simple — just upload the provided files, choose your options, and place the order. 

---

## Modules

### Hardware - Main Components

Core:
- MCU: Nice!Nano v2 or ProMicro (**NRF52840**)
- LoRa Transceiver modules: **HT-RA62 HF** for 868 MHz and **HT-RA62 LF** for 433 MHz (**SX1262**)).

Power:
- Solar Charger: MPPT **CN3791** or **CN3065**

Sensors:
- Current and Voltage Sensor: **INA3221** (three channels)
  - Channel 1 (CH1): nodes & sensors.
  - Channel 2 (CH2): output of the MPPT module.
  - Channel 3 (CH3): solar panel.
- Temperature and Humidity Sensor: **BME280**

Other:
- Solar panel and battery on-off switches
- User and Reset Buttons
- Connectors for Solar Panel, Battery, and Antenna

### Bill of Materials (BOM)

---

## Firmware Settings
Both modules must be configured using the Meshtastic CLI or App to allow serial module pass-through framing:

- For Module 1 (433 MHz)
meshtastic --set serial.enabled true --set serial.baud B115200 --set serial.mode TEXTMSG

- For Module 2 (868 MHz)
meshtastic --set serial.enabled true --set serial.baud B115200 --set serial.mode TEXTMSG

---

## Assembly & Mechanical Build
3D Printing the Frame: Download the structural STL files located in the /hardware/enclosure directory. Print the internal frame plate using PETG or ASA to withstand high internal temperatures during intense summer conditions.

PCB Population: Solder the passive solar components onto the main board following the schematic provided in /hardware/pcb. Mount the two HT-RA62 modules into their respective isolated slots.

RF Separation Setup: Ensure that the 433 MHz and 868 MHz external antennas are physically spaced apart at least 50 cm or oriented perpendicular to each other on the external mounting bracket to limit localized desensitization.

---

## Safety and Important Warnings

⚠️ Never power on the node without an antenna connected. You can permanently damage the LoRa module.

⚠️ Do not power the board via USB and solar/battery at the same time. It may damage your computer’s USB port.

⚠️ If soldering header pins on the LoRa module, make sure the antenna connector doesn’t touch pin 1 (RF output). When mounted directly to the PCB as SMD, this issue doesn’t occur.

---

## Future Improvements



---

## Repository Structure

Rep.
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
