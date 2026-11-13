# LoRa Network for V2V Communication with Autonomous Accident Detection and Predictive Location-Based Hazard Alerts

An infrastructure-independent, retrofit Vehicle-to-Vehicle (V2V) safety communication system engineered on the **ESP32** platform. Designed to prevent secondary multi-car pile-ups in cellular dead zones (tunnels, mountain passes, rural highways), this node combines localized 433 MHz LoRa peer-to-peer broadcasting with automated cellular emergency alerting.

---

## Key Features
* **Autonomous Impact Detection & Fail-Safe Abort:** Uses a hardware-interrupted YL-99 impact sensor paired with a 10-second visual countdown on an OLED display, allowing drivers to cancel false alarms via physical tactile buttons.
* **Zero-Latency Local V2V Mesh (433 MHz LoRa):** Broadcasts decentralized distress payloads (`AUTO_SOS:[NODE_ID],[Lat],[Lng]`) directly to trailing vehicles within milliseconds—requiring zero cellular or internet infrastructure.
* **Dual-Channel Cellular Failover:** Dispatches priority emergency SMS alerts containing live Google Maps coordinate links via a SIM800L GSM modem, followed by automated voice dialing with a 3-stage retry routine.
* **Non-Blocking Firmware Architecture:** Built as an event-driven Finite State Machine (FSM) utilizing `millis()` timing to prevent CPU starvation during continuous NMEA GPS parsing.
* **Custom PCB & 3D Visualization:** Complete schematic capture, multi-bus trace routing, and 3D CAD layout designed in KiCad.

---

## Hardware Architecture & Pin Mapping

| Peripheral | Protocol / Bus | ESP32 GPIO Pins | Baud / Clock | Function |
| :--- | :--- | :--- | :--- | :--- |
| **TTGO T-Call V1.4 (ESP32)** | Core MCU | — | 240 MHz | Main dual-core controller |
| **SX1278 LoRa (Ra-02)** | Hardware SPI | `SCK:18`, `MISO:19`, `MOSI:2`, `SS:13`, `RST:12`, `DIO0:14` | 433 MHz | Peer-to-peer V2V RF broadcast |
| **SIM800L GSM/GPRS** | Hardware UART1 | `RX:26`, `TX:27` | 115200 baud | Emergency SMS & auto-dial |
| **NEO-6M GPS** | Hardware UART2 | `RX:25`, `TX:15` | 9600 baud | Real-time NMEA coordinates |
| **0.96" SSD1306 OLED** | Hardware I2C | `SDA:21`, `SCL:22` | `0x3C` | Telemetry UI & abort countdown |
| **YL-99 Impact Sensor** | Digital GPIO | `GPIO 0` (`INPUT_PULLUP`) | Active `LOW` | Collision trigger |
| **Tactile Switches** | Digital GPIO | `SCROLL:33`, `SELECT:32`, `CANCEL:35` | Pull-Up | Menu navigation & SOS abort |

---

## Firmware State Machine Flow
1. **State 0 (Standby / Telemetry):** Continuously ingests NMEA sentences via `TinyGPS++`, updates live coordinates on the OLED, monitors user menu inputs, and listens for incoming LoRa packets.
2. **State 1 (Pending SOS / 10s Abort Window):** Triggered upon physical impact. Displays an emergency countdown while polling `BTN_CANCEL` every 50ms to prevent false dispatches.
3. **State 2 (Active Emergency Broadcast):** Extracts verified GPS coordinates, transmits cellular SMS + voice call, and continuously broadcasts LoRa hazard packets every 500ms until manually reset.

---

## Repository Structure

```text
Lora-Network-for-V2V-Communication-with-Autonomous-AccidentDetection-/
├── firmware/
│   ├── OmniNode_V2V_Transmitter/
│   │   └── OmniNode_V2V_Transmitter.ino     # Primary vehicle node firmware (FSM, LoRa, GSM, GPS)
│   └── OmniNode_V2V_Receiver/
│       └── OmniNode_V2V_Receiver.ino        # Trailing vehicle receiver node firmware
├── hardware/
│   └── BOM.csv                              # Bill of Materials & component pricing
├── docs/                                    # Upload your Project Report PDF & KiCad renders here
├── .gitignore                               # Ignores KiCad backup files & build cache
└── README.md                                # Project documentation
```

---

## Required Arduino Libraries
* `TinyGPSPlus` by Mikal Hart
* `TinyGSM` by Volodymyr Shymanskyy
* `LoRa` by Sandeep Mistry
* `Adafruit_GFX` & `Adafruit_SSD1306`

---

## Team & Mentorship
* **Developers:** Dhruv S. Karekar, Parth V. Patil, Pranav V. Sangrulkar, Shambhavi V. Kalsekar
* **Project Guide:** Prof. V. V. Khandare
* **Institution:** Department of Electronics & Telecommunication Engineering, KIT's College of Engineering (Autonomous), Kolhapur
