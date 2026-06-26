# ESP32 Development Kit — Custom PCB Design

> A 4-layer KiCad PCB clone of the Espressif ESP32-DevKitC-v4, designed from the official reference schematic.

---

## Table of Contents

- [Overview](#overview)
- [Board Specifications](#board-specifications)
- [Schematic & Design Details](#schematic--design-details)
- [Bill of Materials (BOM)](#bill-of-materials-bom)
- [Layer Stack-Up](#layer-stack-up)
- [Repository Structure](#repository-structure)
- [How to Open in KiCad](#how-to-open-in-kicad)
- [How to Order the PCB](#how-to-order-the-pcb)
- [Design Notes & Customizations](#design-notes--customizations)
- [References](#references)
- [Author](#author)

---

## Overview

This project is a custom clone of the **Espressif ESP32-DevKitC-v4** development board, designed entirely in **KiCad**. The design is based on the official Espressif reference schematic and layout, with minor modifications for ease of hand assembly.

The board features:
- **ESP32-WROOM-32** module with integrated PCB antenna
- **CP2102N** USB-to-UART bridge for serial programming
- **AMS1117-3.3** LDO voltage regulator (3.3 V output)
- **BOOT** and **EN** push buttons
- **2 × 19-pin** header connectors exposing all GPIO pins
- SMD components on both front and back copper layers

**Board Dimensions:** 30.73 mm × 56.90 mm  
**Layer Count:** 4 layers  
**Form Factor:** Matches the original ESP32-DevKitC-v4

---

## Board Specifications

| Parameter            | Value                        |
|----------------------|------------------------------|
| Microcontroller      | ESP32-WROOM-32               |
| USB-UART Bridge      | CP2102N-A01-GQFN28           |
| Voltage Regulator    | AMS1117-3.3 (SOT-223)        |
| Input Voltage        | 5 V via Micro-USB            |
| Output Voltage       | 3.3 V regulated              |
| GPIO Headers         | 2 × 19-pin, 2.54 mm pitch    |
| Board Layers         | 4 (Signal / GND / PWR / Signal) |
| Board Dimensions     | 30.73 mm × 56.90 mm          |
| Component Standard   | SMD (0402 / 0603 packages)   |
| KiCad Version        | KiCad 7 / 8                  |

---

## Schematic & Design Details

The schematic is organized into **five functional blocks**, mirroring the Espressif reference design:

1. **Power Supply** — Micro-USB input → AMS1117-3.3 LDO → 3.3 V rail with decoupling capacitors
2. **USB / USB-UART** — CP2102N bridge with ESD protection diodes (D4, D5, D6) and auto-reset circuit
3. **ESP32 Module** — ESP32-WROOM-32 with all GPIO broken out to headers
4. **Switch Buttons** — BOOT (GPIO0) and EN (chip enable) with RC debounce networks
5. **Connectors** — J2 and J3 (2 × 19-pin headers)

The auto-reset circuit uses two **MMSS8050** NPN transistors (Q1, Q2) driven by the CP2102N's RTS and DTR lines. This allows the Arduino IDE and `esptool.py` to reset and flash the ESP32 without pressing buttons manually.

---

## Bill of Materials (BOM)

| Reference              | Value              | Footprint                                      |
|------------------------|--------------------|------------------------------------------------|
| C1, C2, C21            | 22 µF / 10 V (20%) | Capacitor_SMD:C_0201_0603Metric                |
| C9, C14, C19, C22      | 0.1 µF / 50 V (10%)| Capacitor_SMD:C_01005_0402Metric               |
| C15                    | 0.1 µF / 50 V (NC) | Capacitor_SMD:C_01005_0402Metric               |
| C20                    | 4.7 µF / 6.3 V     | Capacitor_SMD:C_01005_0402Metric               |
| D1                     | LED                | LED_SMD:LED_0603_1608Metric                    |
| D3                     | Schottky Diode     | Diode_SMD:D_SOD-323                            |
| D4, D5, D6             | TVS Diode          | Diode_SMD:D_SOD-523                            |
| J1                     | USB Micro-B        | Connector_USB:USB_MicroAB_Molex_47590-0001     |
| J2, J3                 | 19-pin Male Header | Connector_PinHeader_2.54mm:PinHeader_1x19_...  |
| MOD1                   | ESP32-WROOM-32     | digikey-footprints:ESP32-WROOM-32D             |
| Q1, Q2                 | MMSS8050-H-TP      | digikey-footprints:SOT-23-3                    |
| R2, R24                | 2 KΩ (5%)          | Resistor_SMD:R_01005_0402Metric                |
| R7, R18                | 0 Ω (5%)           | Resistor_SMD:R_01005_0402Metric                |
| R11, R21, R22          | 10 KΩ (5%)         | Resistor_SMD:R_01005_0402Metric                |
| R23                    | 10 KΩ (NC)         | Resistor_SMD:R_01005_0402Metric                |
| R25                    | 22.1 KΩ (5%)       | Resistor_SMD:R_01005_0402Metric                |
| R26                    | 47.5 KΩ (5%)       | Resistor_SMD:R_01005_0402Metric                |
| SW1, SW2               | Push Button        | Button_Switch_SMD:SW_SPST_B3S-1000             |
| U1                     | CP2102N-A01-GQFN28 | Package_DFN_QFN:TQFN-28-1EP_5x5mm_P0.5mm      |
| U2                     | AMS1117-3.3        | Package_TO_SOT_SMD:SOT-223-3_TabPin2           |

> **(NC)** = Not Connected / DNP (Do Not Populate)

---

## Layer Stack-Up

| Layer      | KiCad Name   | Purpose                     |
|------------|--------------|-----------------------------|
| Layer 1    | F.Cu         | Signal routing (top)        |
| Layer 2    | In1.Cu       | Ground plane (GND)          |
| Layer 3    | In2.Cu       | Power plane (3.3 V / 5 V)  |
| Layer 4    | B.Cu         | Signal routing (bottom)     |

A dedicated ground plane on Layer 2 ensures low-impedance return paths, which is critical for the ESP32's RF performance. The power plane on Layer 3 distributes clean 3.3 V across the board.

---

## Repository Structure

```
ESP32-DevKit-Custom/
│
├── ESP32-DevKit.kicad_pro       # KiCad project file
├── ESP32-DevKit.kicad_sch       # Schematic
├── ESP32-DevKit.kicad_pcb       # PCB layout
│
├── Gerbers/                     # Manufacturing files
│   ├── ESP32-DevKit-F_Cu.gtl
│   ├── ESP32-DevKit-In1_Cu.g2
│   ├── ESP32-DevKit-In2_Cu.g3
│   ├── ESP32-DevKit-B_Cu.gbl
│   ├── ESP32-DevKit-F_Mask.gts
│   ├── ESP32-DevKit-B_Mask.gbs
│   ├── ESP32-DevKit-F_Silkscreen.gto
│   ├── ESP32-DevKit-B_Silkscreen.gbo
│   ├── ESP32-DevKit-Edge_Cuts.gm1
│   └── ESP32-DevKit-drill.drl
│
├── BOM/
│   └── ESP32-DevKit-BOM.csv     # Bill of materials
│
├── 3D/
│   └── ESP32-DevKit-3D.png      # 3D render screenshot
│
└── README.md
```

---

## How to Open in KiCad

### Prerequisites

- [KiCad 7 or later](https://www.kicad.org/download/) installed
- Internet connection (to download symbol/footprint libraries if missing)

### Steps

1. **Clone this repository:**
   ```bash
   git clone https://github.com/<your-username>/ESP32-DevKit-Custom.git
   cd ESP32-DevKit-Custom
   ```

2. **Open KiCad:**
   Launch KiCad and click **File → Open Project**, then select `ESP32-DevKit.kicad_pro`.

3. **Open the Schematic:**
   In the KiCad project manager, double-click **Schematic Editor** (`.kicad_sch`) to view the schematic.

4. **Open the PCB Layout:**
   Double-click **PCB Editor** (`.kicad_pcb`) to open the board layout.

5. **Verify the 3D View:**
   In the PCB Editor, go to **View → 3D Viewer** to see the 3D render of the board.

6. **Run DRC (Design Rule Check):**
   In the PCB Editor, go to **Inspect → Design Rules Checker** and click **Run DRC** to verify the design.

---

## How to Order the PCB

### Generating Gerber Files (if not already included)

1. Open the PCB in KiCad PCB Editor.
2. Go to **File → Fabrication Outputs → Gerbers (.gbr)**.
3. Select all layers (F.Cu, In1.Cu, In2.Cu, B.Cu, F.Mask, B.Mask, F.Silkscreen, B.Silkscreen, Edge.Cuts).
4. Click **Plot**.
5. Also generate drill files: **File → Fabrication Outputs → Drill Files (.drl)**.
6. Zip the entire `Gerbers/` folder.

### Ordering from JLCPCB (Recommended)

1. Go to [jlcpcb.com](https://jlcpcb.com) and click **Order Now**.
2. Upload the zipped Gerber files.
3. Set the following options:
   - **Layers:** 4
   - **Dimensions:** Auto-detected (~30.73 × 56.90 mm)
   - **PCB Quantity:** 5 (minimum)
   - **Surface Finish:** HASL (lead-free) or ENIG
   - **Copper Weight:** 1 oz
4. Review the board preview, then proceed to checkout.

> **Note:** 4-layer boards cost slightly more than 2-layer boards. JLCPCB's 4-layer pricing starts from around $2–5 per board for small quantities.

---

## Design Notes & Customizations

This design is faithful to the Espressif ESP32-DevKitC-v4 reference, with the following intentional modification:

- **Voltage regulator (U2 / AMS1117-3.3) placed on the back copper layer (B.Cu)** — In the original Espressif reference, the regulator is on the front. Moving it to the back frees up front-side real estate for small passives (0402 resistors and capacitors), making hand-soldering easier and reducing the risk of component collision.

Other design considerations:
- The ESP32's RF antenna extends **outside** the board edge to avoid copper interference with the 2.4 GHz signal.
- TVS diodes (D4, D5, D6) protect the USB data lines against ESD events.
- RC networks on BOOT and EN pins prevent spurious resets due to contact bounce.
- The footprint for **MOD1 (ESP32-WROOM-32)** was sourced from **SnapEDA**, as it is not included in KiCad's default libraries.

---

## References

- [Espressif ESP32-DevKitC-v4 Reference Design](https://www.espressif.com/en/support/documents/technical-documents?keys=ESP32-Devkit) — Official schematics, PCB layout, BOM, and Gerber files
- [ESP32-WROOM-32 Datasheet](https://www.espressif.com/sites/default/files/documentation/esp32-wroom-32_datasheet_en.pdf)
- [CP2102N Datasheet — Silicon Labs](https://www.silabs.com/documents/public/data-sheets/cp2102n-datasheet.pdf)
- [AMS1117-3.3 Datasheet](http://www.advanced-monolithic.com/pdf/ds1117.pdf)
- [KiCad Documentation](https://docs.kicad.org/)
- [SnapEDA — ESP32-WROOM-32 Symbol & Footprint](https://www.snapeda.com/)

---

## Author

**Chacko Jose**  
B.Tech Electronics & Communication Engineering, Semester 2  
School of Engineering, CUSAT, Kochi  
GitHub: [@chackojose2005-zodiac](https://github.com/chackojose2005-zodiac)

---

*This project was completed as part of Week 4 — Project 6 of the embedded systems PCB design curriculum.*
