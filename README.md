# High-Isolation Optocoupler Relay Driver Module

A professional, industrial-grade relay driver module designed for switching high-current external loads safely while providing complete galvanically isolated protection for sensitive low-voltage microcontrollers (ESP32, Arduino, STM32, Raspberry Pi).

---

## Key Features

- **Galvanic Electrical Isolation:** Utilizes an **PC817 Optocoupler** to completely decouple the low-voltage MCU control signal from the high-power relay switching domain.
- **Physical Isolation Slot:** Features a routed PCB slot/cutout directly beneath the optocoupler chip to eliminate surface creepage currents and maximize voltage isolation.
- **Dedicated Power Domains ("Two Islands Strategy"):** Separate ground structures (`MCU_GND` vs. `RELAY_GND`) prevent high-current switching noise and inductive spikes from reaching the controller.
- **Inductive Kickback Protection:** Integrated **1N4148 / 1N4007 Flyback Diode** across the relay coil absorbs voltage spikes generated during coil de-energization.
- **Visual Status Indicator:** Integrated low-current LED indicator providing immediate visual feedback when the control signal is active.

---

## Circuit Specifications

| Parameter | Specification / Value |
| :--- | :--- |
| **Logic Input Voltage** | 3.3V – 5V DC |
| **Relay Operating Voltage** | 5V DC |
| **Logic Drive Current** | ~10 mA – 15 mA |
| **Isolation Chip** | PC817 Optocoupler |
| **Switching Transistor** | 2N3904 / S8050 NPN |
| **Output Terminals** | Common (COM), Normally Open (NO), Normally Closed (NC) |
| **Max Switching Voltage** | 250V AC / 30V DC (Relay dependent) |
| **Max Switching Current** | Up to 10A |

---

## System Architecture ("Two Islands")

```text
========================================================================
   [ ISLAND 1: MCU CONTROL SIDE ]       |      [ ISLAND 2: RELAY POWER SIDE ]
   (Low-Voltage, Sensitive MCU Ground)  |      (High-Power, Isolated Relay Ground)
========================================================================

  [J1 Input Terminal]                  
   ├── Pin 1 (Signal) ──> [R3 (220Ω)] ──> [PC817 Pin 1 (Anod.)] 
   │                                                                 │ 
   │                                     == THE BRIDGE ==            │ (Light signal passes 
   │                                     == (PC817 Chip) ==          │  safely across air gap)
   │                                                                 ▼ 
   └── Pin 2 (MCU GND) ─────────────────> [PC817 Pin 2 (Cath.)]   [PC817 Pin 3 (Emitt.)] ──> [RELAY_GND]
                                                                     │
                                         [PC817 Pin 4 (Collec.)] ──> [R2 (1kΩ)] ──> [Transistor Q2 Base]
                                                                                           │
                                                                           ┌───────────────┴───────────────┐
                                                                           ▼                               ▼
                                                                [Transistor Q2 Collector]       [VCC_5V Power Rail]
                                                                           │                               │
                                                                           ├── [Flyback Diode D1 Cathode]  │
                                                                           │   (Striped Side)              ├── [Flyback Diode D1 Anode]
                                                                           │                               │   (Non-Striped Side)
                                                                           ├── [Relay Coil Pin 2]          │
                                                                           │                               └── [Relay Coil Pin 1]
                                                                           │
                                                                           ▼
                                                                  [Relay Switch Contacts]
                                                                     ├── Pin 3 (COM) ──> [J2 Terminal Pin 2]
                                                                     ├── Pin 4 (NC)  ──> [J2 Terminal Pin 1]
                                                                     └── Pin 5 (NO)  ──> [J2 Terminal Pin 3]
========================================================================
```

---

## Bill of Materials (BOM)

| Designator | Quantity | Component Description | Footprint / Package |
| :--- | :--- | :--- | :--- |
| **U4** | 1 | PC817 Optocoupler | DIP-4 / SMD-4 |
| **RLY1** | 1 | 5V DC Relay (SRD-05VDC-SL-C) | THT 5-Pin |
| **Q2** | 1 | 2N3904 NPN Transistor | TO-92 / SOT-23 |
| **D1** | 1 | 1N4148 / 1N4007 Diode | DO-35 / SOD-123 |
| **U1** | 1 | Red LED (Status Indicator) | 0805 / 3mm THT |
| **R3** | 1 | 220Ω Resistor ($\pm 5\%$, 1/4W) | 0805 / Axial |
| **R1** | 1 | 330Ω Resistor ($\pm 5\%$, 1/4W) | 0805 / Axial |
| **R2** | 1 | 1kΩ Resistor ($\pm 5\%$, 1/4W) | 0805 / Axial |
| **J1** | 1 | 2-Pin Screw Terminal (MCU Input) | 5.08mm Pitch |
| **J2** | 1 | 3-Pin Screw Terminal (High-Load Output)| 5.08mm Pitch |
| **J3** | 1 | 2-Pin Screw Terminal (Relay Power 5V) | 5.08mm Pitch |

---

## PCB Design & Layout Rules

1. **Trace Width Standards:**
   - **Control Signals (`MCU_SIGNAL`):** `12 mil` (0.3mm)
   - **Relay Power & Coil (`VCC_5V`, `RELAY_GND`):** `25 mil` (0.635mm)
   - **High-Current Output (`COM`, `NO`, `NC`):** `50 mil` (1.27mm) or poured copper planes.
2. **Clearance & Isolation:**
   - Physical board cutout slot drawn on the **Board Outline** layer beneath `U4` (PC817).
   - Strict >3mm trace clearance maintained between control domain and power domain.

---

## Pinout & Connection Guide

### 1. Control Input (J1)
- **Pin 1 (`MCU_SIGNAL`):** Connect to Microcontroller GPIO Pin.
- **Pin 2 (`MCU_GND`):** Connect to Microcontroller Ground.

### 2. Isolated Power Input (J3)
- **Pin 1 (`5V_INPUT`):** Connect to External +5V Power Source (+).
- **Pin 2 (`RELAY_GND`):** Connect to External Power Ground (-).

### 3. Load Output Terminal (J2)
- **Pin 1 (`NC`):** Normally Closed Contact.
- **Pin 2 (`COM`):** Common Terminal.
- **Pin 3 (`NO`):** Normally Open Contact.

---

## License & Usage

This hardware design is open-source and released under the **MIT License**. Feel free to modify, manufacture, or integrate it into your own custom projects!
