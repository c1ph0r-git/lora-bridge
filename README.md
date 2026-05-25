# Dual-Band Solar LoRa Mesh

Lora-Bridge: Unified Dual-Band (433 and 868 MHz) Solar Node for Meshtastic / Meshcore

[![License: CERN OHL-S v2](https://img.shields.io/badge/License-CERN%20OHL--S%20v2-blue.svg)](https://ohwr.org/cern_ohl_s_v2.txt)
[![Meshtastic](https://img.shields.io/badge/Meshtastic-Compatible-green.svg)](https://meshtastic.org)
[![Meshcore](https://img.shields.io/badge/Meshcore-Compatible-green.svg)](https://meshcore.io)
[![Hardware](https://img.shields.io/badge/Hardware-Open%20Source-orange.svg)](#)

---

## Disclaimer

This is a prototype board under development. I take no responsability for its misuse. 

---

## About

**Lora-Bridge** is an open-source, community-driven, solar-powered dual-band LoRa relay node. It merges the elegant architecture design of the **MASN** (Meshtastic Autonomous Solar Node) with a **Faketec-inspired** design and a hardware-integrated **dual-band cross-frequency bridge** system. 

Unlike standard single-frequency nodes, this design bridges the gap between distinct Meshtastic/Meshcore networks by establishing a local hardware **UART bridge** between two dedicated **LoRa** nodes: one operating on **433 MHz (LF)** and the other on **868 MHz (HF)**.

Using this repeater bridge, client nodes can use either frequency to access the network and talk to others in a different frequency without requiring a second node, which improves the scope of communication, as well as the resilience of the network.

---

## Key Features

- **Dual LoRa Modules:** Optimized exclusively around two identical Heltec HT-RA62 Semtech SX1262-based modules (1x Low-Frequency 433 MHz and 1x High-Frequency 868 MHz), ensuring balanced power consumption and identical RF driver logic.
- **Cross-Frequency UART Bridge:** Hardware serial interconnect loop acting as an autonomous cross-band repeater. Packets arriving on 433 MHz are parsed and repeated immediately over 868 MHz, and vice versa.
- **Solar Core Power Path:** Complete MPPT solar charging circuit optimized for high efficiency during low-light winter days, integrated with comprehensive over-current and over-charge protections for Lithium batteries.
- **Low Power Consumption:** The nRF52840 MCU of the promicro / nice!nano boards has a very low power consumption, ideal for a solar design.
- **Sensor Integration and Telemetry:** This design integrates voltage/current sensor for the solar panel, battery and load, as well as enviromental sensor for temperature and humidity inside the enclosure. 
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

![schematic](/pcb/schematic/Schematic_LoraMesh-dual-band-(433-and-868MHz)_2026-05-25.png)

#### Interconnection & Bridge Configuration

The cross-frequency bridging architecture utilizes a bidirectional Serial/UART passthrough mechanism. Packets originating from the low-frequency 433 MHz grid are received by the primary HT-RA62, transmitted out its hardware TX pin, and injected natively into the secondary HT-RA62's RX pin to be broadcasted instantly to the 868 MHz network.

![promicro](images/ProMicroNRF52840_Foot.webp)

#### UART Pinout Interconnect
To set up the physical UART-1 bridge between the two nRf52840 modules (promicro or nice!nano), route the cross-over communication as follows:

| Module 1 (433 MHz LF) | Module 2 (868 MHz HF) | Description |
| :--- | :--- | :--- |
| `TX` (Pin 3: P0.08) | `RX` (Pin 2: P0.06) | 433 MHz Traffic to 868 MHz Grid |
| `RX` (Pin 2: P0.06) | `TX` (Pin 3: P0.08) | 868 MHz Traffic to 433 MHz Grid |
| `GND` | `GND` | Common Ground Reference |

### PCB Design and Layout

![pcb](images/pcb_top.svg) ![pcb](images/pcb_bottom.svg)

### Dimensions and Mounting

- PCB Size: 65 mm × 120 mm
- Mounting Holes: M2.5

### Ordering the PCB - Gerber File

You can order the PCB directly from **JLCPCB** or PCBWay (the design files are linked below).
Upload the provided files, choose your options, and place the order. 

Download [here](https://github.com/c1ph0r-git/lora-bridge/tree/main/pcb/gerber)

---

## Hardware Modules

### Modules - Main Components

Core:
- MCU: Nice!Nano v2 or ProMicro (**NRF52840**)
- LoRa Transceiver modules: **HT-RA62 HF** for 868 MHz and **HT-RA62 LF** for 433 MHz (**SX1262**).

![nrf](images/bom-promico-mcu-300x300.webp) ![lora](images/ht-ra62-hf-lora-300x300.webp)

Power:
- Solar Charger: MPPT **CN3791** or **CN3065** 

![cn3791](images/bom-cn3791-solar-charger-300x300.webp) 

Sensors:
- Current and Voltage Sensor: **INA3221** (three channels)
  - Channel 1 (CH1): nodes & sensors
  - Channel 2 (CH2): output of the MPPT module
  - Channel 3 (CH3): solar panel
- Temperature and Humidity Sensor: **BME280** or **BMP280** (cheaper)

![ina3221](images/bom-ina3221-current-sensor-300x300.webp) ![bme](images/bom-bme-250-env-sensor-300x300.webp) 

Other PCB assembly components:
- Solar panel and battery on-off switches:
  - SS12D10 Switches
- User and Reset Buttons:
  - Push Buttons 3×6×5 mm
- Connectors for Solar Panel and Battery: 
  - 2P Screw Terminals for Battery/Solar
  - 2P JST PH 2.0 mm Battery Connector
- Headers: 
  - 40-pin Straight Headers 2.54 mm
  - 40-pin 90° Headers 2.54 mm

![SS12D10](images/bom-SS12D10-switch-300x300.webp) 
![Buttons](images/bom-push-button-300x300.webp) 
![Screw](images/bom-terminal-connector-300x300.webp) 
![JST](images/bom-JST-PH2-battery-connector-300x300.webp) 
![Straight](images/bom-pin-300x300.webp) 
![Headers](images/bom-90-deg-pin-300x300.webp)

### Hardware - Other Essencial Components

- Antenna Cables: either UFL/IPX to SMA or IPX to N-Type Female (10cm)
- 433 and 868 MHz Antennas: recommend Ziisor 4.5 dBi 40cm (N-Type) or other 
- 5V Solar Panel: like NIVIAN 9W from Amazon
- Battery: ~10,000mAh (~8 days). If the sensors are disabled the battery life can be extended a lot. Conservative calculation [here](https://github.com/c1ph0r-git/lora-bridge/blob/main/battery.md)  

### Hardware - Enclosure

- Weatherproof Electrical Box 158×90×60mm IP65
- Vent Plug M5×0.8-7 IP67
- Cable Gland M12 IP68
- Solar Panel Cable

### Bill of Materials (BOM)

#### All PCB Components (BOM)

| Part | Qty. | Cost | Source | Notes | 
| :----------- |:--------------|:--------------|:--------------|:--------------|  
| Dual-Band PCB	| 1	| 7€	| [Download](https://github.com/c1ph0r-git/lora-bridge/tree/main/pcb/gerber))	|  |
| Promicro/NiceNano (NRF52840)	| 1	| 3€	| [AliExpress]()	| Choose wisely, some have bugs |
| HT-RA62-HF 868 LoRa |	1 |	6,74€ |	[AliExpress]()	| |
| HT-RA62-LF 433 LoRa |	1 |	6,74€ |	[AliExpress]()	| |
| MPPT CN3065 Charger	| 1	| 2,20€	| [AliExpress]()	| |
| INA3221 Current Sensor	| 1 |	1,72€ |	[AliExpress]()	| Buy the purple one, not the black |
| BMP280 Temperature/Humidity Sensor	| 1	| 0,94€ |	[AliExpress]()	| Choose 6-pin, 3.3 V version |
| 40-pin Straight Headers 2.54 mm	| 4	| 2,8€	| [AliExpress]() | |	
| 40-pin 90° Headers 2.54 mm	| 1	|	0,7€ | [AliExpress]()	| |
| 2P Screw Terminals for Battery/Solar	| 2	| 1,80€	| [AliExpress]() | 	|
| 2P JST PH 2.0 mm Battery Connector	| 1	| 1,62€ |	[AliExpress]() | |	
| SS12D10 Switches	| 2	| 0,99€ |	[AliExpress]() | |	
| Push Buttons 3×6×5 mm	| 4	| 1,8€ |	[AliExpress]()	| |

#### Other Essencial Components (BOM)

| Part | Qty. | Cost | Source | Notes | 
| :----------- |:--------------|:--------------|:--------------|:--------------|  
| NIVIAN 9W 5V Solar Panel	| 1	| 6,89€	| [Amazon]() |	|
| 21700 Cell | 2 | XX€ | [NKON]() | Choose this or other reputable seller; e.g., Samsung 50E in Parallel (1S2P)	 |

Antenna and Cable Options:

N-Type
| Part | Qty. | Cost | Source | Notes | 
| :----------- |:--------------|:--------------|:--------------|:--------------|  
| IPX to N-Type Female (10cm) | 2 | XX€ | [AliExpress]() | 10cm |
| Ziisor 433Mhz 4.5dBi 40cm (N-Type) | 1 | XX€ | [AliExpress]() | |
| Ziisor 868Mhz 4.5dBi 40cm (N-Type) | 1 | xX€ | [AliExpress]() | | 

SMA
| Part | Qty. | Cost | Source | Notes | 
| :----------- |:--------------|:--------------|:--------------|:--------------|  
| UFL/IPX to SMA | 1 | x€ | [AliExpress]() | 10 cm |
| 868MHz Antenna GIZONT 17cm SMA | 1 | xx€ | [AliExpress](https://pt.AliExpress.com/item/1005004607615001.html?gatewayAdapt=usa2bra4itemAdapt) | | 
| 433MHz Antenna Rabbit-Labs or Ziisor 4dBm SMA | 1 | xx€ | [AliExpress]() | |

#### Enclosure (BOM)

| Part | Qty. | Cost | Source | Notes | 
| :----------- |:--------------|:--------------|:--------------|:--------------|  
| Weatherproof Electrical Box 158×90×60mm IP65	| 1	| 5,69€ |	[AliExpress]() | |	
| Vent Plug M5×0.8-7 IP67 |	1	| 2,76€ |	[AliExpress]()	| |
| Cable Gland M12 IP68	| 1	| 1,67	| [AliExpress]()	| Fits 3 – 6.5 mm cables |

### Overall Cost

This gross estimate does not consider required time, solder iron, flux, solder, glue, galvanazing tape, capton tape, silicone, drill bits, brackets.
Nor does it include shipping and VAT.

---

## Firmware Setup - Before Assembly!

Before soldering anything, it’s a good idea to make sure the microcontroller works properly and boots without issues. This quick step helps you avoid problems later in the build.

### Check or Update the Bootloader
To install the Meshcore or Meshtastic firmware, your microcontroller must have a bootloader version 0.8 or higher. You can check it by following these steps:

- Connect the NiceNano (NRF52840) board via USB.
- Make two quick touches (using metal tweezers) between the RESET and GND pins (see reference photo below) to enter DFU mode.
- Your computer will mount a USB drive named NICENANO or similar.
- Check that the bootloader version is 0.8 or higher. You can verify this by opening the file INFO_UF2.TXT inside the USB drive that appears when entering DFU mode.
  - If it is inferior, copy the file [update-nice_nano_bootloader-0.9.2_nosd.uf2](https://github.com/adafruit/Adafruit_nRF52_Bootloader/releases/download/0.9.2/update-nice_nano_bootloader-0.9.2_nosd.uf2) into the drive.
  - If you need another version, check the [official repository](https://github.com/adafruit/Adafruit_nRF52_Bootloader).

After copying, the board will automatically reboot (give it a few seconds).
If your board doesn’t include a bootloader, follow the official procedure linked in the [bootloader guide](https://github.com/gargomoma/fakeTec_pcb?tab=readme-ov-file#my-promicro-is-dead-what-can-i-do).

### Install the Firmware

- Enter DFU mode (as explained above).
- Open https://flasher.meshtastic.org or https://meshcore.co.uk/flasher.html. I recommend: [meshcore-lusofw](https://flasher.meshcore.pt/?_gl=1*1oh6xog*_ga*MTEzNDY1MDk0Ni4xNzc5MjI3NDg0*_ga_HF6KDP3ZSV*czE3Nzk3NDUzNjUkbzExJGcxJHQxNzc5NzQ1MzY3JGo1OCRsMCRoMTUwODUzODMxNg..)
- Select the device NRF52 Pro-micro DIY / faketec.
- Choose the latest stable version (or beta if you want to test new features).
- Download the file and drag it into the DFU USB drive.

Once copied, the board will reboot automatically and start running the Meshtastic firmware.
After this process, you’ll know your microcontroller is healthy and ready to integrate into the node.

### Firmware Settings
Both nrf52840 modules must be configured using the Meshtastic CLI or App to allow serial module pass-through framing:

- For Module 1 (433 MHz)
meshtastic --set serial.enabled true --set serial.baud B115200 --set serial.mode TEXTMSG

- For Module 2 (868 MHz)
meshtastic --set serial.enabled true --set serial.baud B115200 --set serial.mode TEXTMSG

---

## Assembly & Mechanical Build

PCB Population: Solder the passive solar components onto the main board following the schematic provided in /hardware/pcb. Mount the two HT-RA62 modules into their respective isolated slots.

RF Separation Setup: Ensure that the 433 MHz and 868 MHz external antennas are physically spaced apart at least 50 cm or oriented perpendicular to each other on the external mounting bracket to limit localized desensitization.

---

## Safety and Important Warnings

⚠️ Never power on the node without an antenna connected. You can permanently damage the LoRa module.

⚠️ Do not power the board via USB and solar/battery at the same time. It may damage your computer’s USB port.

⚠️ If soldering header pins on the LoRa module, make sure the antenna connector doesn’t touch pin 1 (RF output). When mounted directly to the PCB as SMD, this issue doesn’t occur.

---

## Future Improvements

[] Decrease PCB size to a maximum of 100mm
[] Include capacitors on LoRa modules and I2C lines
[] Include capacitors and resistors on buttons to improve stability (avoid bouncing) and filter noise
[] Include header for tft screen, buzzer, vibrator motor via mosfet circuit to decrease power consumption
[] Include a battery maximum discharge protection circuit  

---

## Repository Structure

```
Rep.
├── firmware/              # Bridge communication configs and node flash binaries
├── hardware/
│   ├── pcb/               # KiCad schematics, layout design, and Gerber files  
│   └── enclosure/         # Faketec-style industrial 3D prints and step files
├── docs/                  # Detailed documentation and RF guidelines
└── README.md              # Project overview
```

## Contributing
Contributions are heavily welcomed! If you are optimizing the RF trace filters for the HT-RA62 or improving the serial bridging packet structure, please feel free to open an Issue or submit a Pull Request.

## License
This project is licensed under the CERN Open Hardware Licence Version 2 – Strongly Reciprocal (CERN OHL-S v2). You are free to copy, modify and distribute this design, provided you maintain identical licensing protections on any derivative works. See the LICENSE file for full conditions.

## Acknowledgments
Heavily inspired by Daniel P. Costas' [MASN Node](https://danielpcostas.dev/masn-a-simple-and-open-source-solar-node-for-meshtastic/). 

Bridging architecture concept derived from Meshcore Portugal [(WSL3 Tutorials)](https://www.meshcore.pt/en/docs/tutorials/bridge-wsl3).

Mechanical aesthetics adapted from [Faketec](https://github.com/gargomoma/fakeTec_pcb) node design.
