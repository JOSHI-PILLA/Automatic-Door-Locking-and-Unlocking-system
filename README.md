# 🚪 Automatic Door Locking and Unlocking System

A sensor-based smart access control system built around **Arduino Uno**, comparing three different sensing technologies — **PIR**, **Ultrasonic (HC-SR04)**, and **IR Break-Beam** — to automatically lock/unlock a door and show live status on an OLED display.

Simulated and validated using **Tinkercad Circuits** (PIR design) and **Cirkit Designer** (Ultrasonic and IR designs).

> Project by **Pilla Joshi** — B.Tech, Electronics and Communication Engineering

---

## 📋 Overview

Traditional locks rely on manual keys or keypads, which can be lost, duplicated, or forgotten. This project uses sensor-based detection to identify a person or object near a door and automatically drives a servo motor to lock or unlock it, with real-time feedback on an OLED display.

Three independent sensor variants were designed and simulated so the same core control logic (**sense → decide → act → display**) could be evaluated across different sensing technologies, and so the best sensor could be recommended for different regions of a house.

### Objectives
- Design and simulate automatic door control using three different sensor technologies.
- Compare PIR, Ultrasonic, and IR sensors to find the most appropriate sensor for different entry points.
- Provide real-time visual feedback of door status (Opened/Closed) via OLED.
- Control a servo motor that represents the physical locking/unlocking mechanism.

### Scope
Designed as a low-cost, scalable smart-access solution for residential main doors, garages, gates, cabinets, and interior room doors. Implemented and tested via circuit simulation, but the same design/code can be deployed on physical Arduino hardware with minimal modification.

---

## 🧭 Choosing the Right Sensor for the Right Region

Sensor choice isn't just about detection range — it's about how much uncontrolled foot traffic passes near a door. PIR and IR sensors can't tell the difference between someone walking past and someone approaching to enter, so on high-traffic doors they risk false unlocking. Ultrasonic sensors measure exact distance, so a tight threshold (e.g. <15 cm) ensures the door only reacts to someone genuinely standing at it.

| House Region | Recommended Sensor | Why |
|---|---|---|
| Ground floor main entrance (high foot traffic) | **Ultrasonic (HC-SR04)** | Precise distance measurement prevents false unlocking from passersby |
| Garage door / gate | **Ultrasonic (HC-SR04)** | Detects exact vehicle distance before opening |
| Low-traffic interior room door | **PIR Sensor** | Wide-angle detection is fine — traffic is limited to household members |
| Narrow interior doorway / cabinet / locker | **IR Break-Beam** | Reliable binary detection in a single-file, low-traffic passage |
| Upper-floor balcony / low-footfall backyard | **PIR Sensor** | Not exposed to public traffic, so PIR's wider cone isn't a security gap |

### Sensor Comparison

| Parameter | PIR Sensor | Ultrasonic Sensor | IR Break-Beam |
|---|---|---|---|
| Detection principle | Passive infrared / body heat | Sound wave echo (time-of-flight) | IR light beam interruption |
| Range | ~6–7 m, wide cone | 2 cm – 400 cm, narrow beam | A few cm – few m (line of sight) |
| Precision | Low (area-based) | High (exact distance in cm) | High (binary: blocked/clear) |
| False-trigger risk | High in busy areas | Low — distance threshold filters passersby | Low, but only within beam path |
| Affected by | Heat sources, pets, sunlight | Soft/sound-absorbing surfaces | Ambient IR light, misalignment |
| Ideal region | Low-traffic interior rooms, upper floors | Ground floor entrance, garages, gates | Narrow, low-traffic doorways, cabinets |
| Cost | Low | Low–Medium | Low |

---

## 🛠️ Hardware Components

| # | Component | Qty | Purpose |
|---|---|---|---|
| 1 | Arduino Uno | 1 | Main microcontroller |
| 2 | PIR Motion Sensor (HC-SR501 / Parallax PIR) | 1 | Motion detection for entrance configuration |
| 3 | Ultrasonic Sensor (HC-SR04) | 1 | Distance measurement for approaching objects/vehicles |
| 4 | IR Break-Beam Sensor | 1 | Presence detection via beam interruption |
| 5 | SG90 Micro Servo Motor | 1 | Door locking/unlocking actuator |
| 6 | 0.96" OLED Display (SSD1306, I2C, 128×64) | 1 | Real-time door status and distance readout |
| 7 | Red & Green LEDs | 2 | Visual lock/unlock indicators (PIR configuration) |
| 8 | 220Ω Resistors | 2 | Current-limiting for LEDs |
| 9 | Breadboard | 1 | Prototyping / power distribution |
| 10 | Jumper Wires (M–M) | As required | Circuit connections |
| 11 | USB Cable / 5V Power Supply | 1 | Power source for Arduino Uno |

## 💻 Software / Tools

| Tool | Type | Purpose |
|---|---|---|
| [Tinkercad Circuits](https://www.tinkercad.com/circuits) | Web simulator | Design & simulate the PIR-based circuit |
| [Cirkit Designer](https://app.cirkitdesigner.com) | Web simulator | Design & simulate the Ultrasonic and IR-based circuits |
| Adafruit SSD1306 Library | Arduino library | Drives the OLED over I2C |
| Adafruit GFX Library | Arduino library | Core graphics/text rendering for the OLED |
| `Servo.h` | Arduino built-in | Controls servo rotation angle |
| `Wire.h` | Arduino built-in | I2C communication with the OLED |

---

## ⚙️ System Design & Working Principle

All three variants follow the same control loop:

1. **Sense** – The active sensor (PIR / Ultrasonic / IR) continuously monitors its surroundings.
2. **Decide** – The Arduino checks the reading against a threshold (motion HIGH for PIR, distance < 10–15 cm for Ultrasonic, beam-broken LOW for IR).
3. **Act** – The servo rotates to 90° (unlocked/open) or returns to 0° (locked/closed).
4. **Display** – The OLED (or Serial Monitor, in early tests) shows "Door is Opened" / "Door is Closed", plus live distance for the ultrasonic variant.

---

## 1️⃣ PIR Sensor Design (Tinkercad)

Wide-area motion detection, ideal for a main entrance. Uses red/green LEDs to indicate locked/unlocked state, plus servo actuation.

**Circuit Connections**

| Component | Pin | Arduino Pin |
|---|---|---|
| PIR Sensor | OUT | Digital Pin 9 |
| PIR Sensor | VCC / GND | 5V / GND |
| Servo Motor | Signal | Digital Pin 6 |
| Servo Motor | VCC / GND | 5V / GND |
| Red LED | Anode (via 220Ω) | Digital Pin 2 |
| Green LED | Anode (via 220Ω) | Digital Pin 4 |

**Result:** Door locked (red LED ON) when no motion is detected; unlocks and green LED turns ON when motion is detected.

---

## 2️⃣ Ultrasonic Sensor Design (Cirkit Designer)

Suited for garages/gates where the exact distance of an approaching object or vehicle matters. Displays live measured distance on the OLED.

**Circuit Connections**

| Component | Pin | Arduino Pin |
|---|---|---|
| HC-SR04 Ultrasonic Sensor | Trig | Digital Pin 8 |
| HC-SR04 Ultrasonic Sensor | Echo | Digital Pin 7 |
| HC-SR04 Ultrasonic Sensor | VCC / GND | 5V / GND |
| Servo Motor | Signal | Digital Pin 3 |
| Servo Motor | VCC / GND | 5V / GND |
| OLED Display | SDA / SCL | A4 / A5 |
| OLED Display | VCC / GND | 5V / GND |

**Result:** Door opens (servo → 90°) when distance < 15 cm; closes (servo → 0°) otherwise. OLED shows live distance in cm.

---

## 3️⃣ IR Break-Beam Sensor Design (Cirkit Designer)

Best for narrow, low-traffic doorways, cabinets, or lockers, using simple binary beam detection. Implemented in two stages: an initial Serial Monitor test version, and a final OLED version.

**Circuit Connections**

| Component | Pin | Arduino Pin |
|---|---|---|
| IR Break-Beam Sensor | OUT | Digital Pin 7 |
| IR Break-Beam Sensor | VCC / GND | 5V / GND |
| Servo Motor | Signal | Digital Pin 3 |
| OLED Display | SDA / SCL | A4 / A5 |
| OLED Display | VCC / GND | 5V / GND |

**Result:** Door opens when the IR beam is interrupted, closes when the beam is clear, with status shown on OLED (and, in the initial test version, on the Serial Monitor).

---

## 📊 Results Summary

| Sensor Variant | Trigger Condition | Door State Observed | Feedback Method |
|---|---|---|---|
| PIR | Motion detected (HIGH) | Servo → 90°, green LED ON | LED indicators |
| PIR | No motion (LOW) | Servo → 0°, red LED ON | LED indicators |
| Ultrasonic | Distance < 15 cm | Servo → 90°, "Opened" shown | OLED (with live distance) |
| Ultrasonic | Distance ≥ 15 cm | Servo → 0°, "Closed" shown | OLED (with live distance) |
| IR Break-Beam | Beam interrupted (LOW) | Servo → 90°, "Opened" | OLED / Serial Monitor |
| IR Break-Beam | Beam clear (HIGH) | Servo → 0°, "Closed" | OLED / Serial Monitor |

All three configurations were successfully simulated and correctly transitioned between locked/closed and unlocked/open states based on their respective sensor inputs.

### ✅ Advantages
- Low-cost, easily reproducible design using widely available components.
- Modular sensor approach — the same servo/OLED logic is reused across all three sensor types.
- Real-time visual feedback improves usability over a purely mechanical lock.
- Flexibility to select the most appropriate sensor per house region rather than one-size-fits-all.

### ⚠️ Limitations
- PIR sensors can produce false triggers from pets or nearby heat sources.
- Ultrasonic sensors may give inconsistent readings on soft or sound-absorbing surfaces.
- IR break-beam sensors require precise physical alignment between transmitter and receiver.
- The current design is simulation-based; real-world deployment requires weatherproofing and a backup manual override for safety.

---

## 🚀 Future Scope

- Keypad or RFID-based manual override for access.
- Integration with a mobile app via Wi-Fi/Bluetooth (e.g., ESP32) for remote monitoring.
- Battery backup with solar charging for outdoor deployments.
- Data-logging of entry/exit events for security auditing.

## 🏢 Applications

- Automatic entry doors for homes, offices, shops, and hospitals.
- Touch-free / hygienic door access for public buildings and restrooms.
- Automatic gates for parking lots and secure entry points.
- Accessibility aid for elderly or physically challenged individuals.
- Base model for automatic bins and garage doors.

---

## 📚 References

- [Arduino Official Documentation](https://docs.arduino.cc)
- [Adafruit SSD1306 & GFX Library Documentation](https://learn.adafruit.com/monochrome-oled-breakouts)
- [Tinkercad Circuits](https://www.tinkercad.com/circuits)
- [Cirkit Designer](https://app.cirkitdesigner.com)
- HC-SR04 Ultrasonic Sensor Datasheet
- Parallax PIR Sensor (555-28027) Datasheet

## AUTHOR
**PILLA JOSHI B.Tech Electronics & Communications Engineering**
