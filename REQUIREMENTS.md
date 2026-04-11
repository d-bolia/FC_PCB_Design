# Flight Controller PCB — Requirements Document
**Project:** Custom STM32-based Drone Flight Controller  
**Designer:** Desmond Bolia  
**Revision:** v0.1  
**Date:** April 11, 2026  
**Reference Design:** Pixhawk 6C (Holybro)

---

## 1. Mission Context

This board is designed for autonomous quadcopter operation in GPS-available 
and GPS-degraded environments. It is derived from the author's senior capstone 
project — a fiber-optic-tethered autonomous UAV with onboard AI inference and 
MAVLink telemetry — and targets ArduPilot/PX4 firmware compatibility.

---

## 2. Electrical Requirements

| Parameter | Requirement |
|---|---|
| Input voltage | 3S–4S LiPo (9.9V – 16.8V) |
| Regulated rails | 5V (peripheral), 3.3V (MCU/sensors) |
| Max continuous current (5V rail) | 1.5A |
| Power protection | Reverse polarity, TVS transient protection |
| Battery monitoring | Voltage + current sense via ADC |

---

## 3. Microcontroller

| Parameter | Selection | Rationale |
|---|---|---|
| MCU | STM32F405RGT6 | Well-documented, ArduPilot native, QFP64 hand-solderable |
| Core | ARM Cortex-M4 @ 168MHz | Sufficient for flight control loop |
| Flash | 1MB | Fits ArduPilot firmware |
| Package | LQFP64 | Hand-solderable, widely available |

*Note: STM32H743 used in Pixhawk 6C — selected F405 for reduced complexity 
on first PCB design iteration.*

---

## 4. Sensors

| Sensor | Interface | Function |
|---|---|---|
| ICM-42688-P | SPI1 | Primary IMU (accel + gyro) |
| BMP388 | I2C | Barometric altimeter |

---

## 5. Communication Interfaces

| Interface | Peripheral | Connector | Function |
|---|---|---|---|
| SPI1 | ICM-42688-P | — | IMU data |
| USART1 | GPS | JST-GH 6-pin | GPS module |
| USART2 | Telemetry | JST-GH 6-pin | MAVLink / GCS |
| USART3 | Debug | Header | Firmware debug |
| I2C1 | BMP388 | — | Barometer |
| USB | OTG-FS | USB-C | Firmware flashing, config |

---

## 6. Motor Outputs

| Parameter | Requirement |
|---|---|
| Number of outputs | 4 (quadcopter) |
| Protocol | DSHOT300 / PWM |
| Signal level | 3.3V logic |
| Connector | Solder pads |

---

## 7. Form Factor

| Parameter | Requirement |
|---|---|
| Board dimensions | 40mm x 40mm |
| Mounting pattern | 30.5mm x 30.5mm standard |
| Mounting holes | M3 |
| Layer count | 4-layer |
| PCB thickness | 1.6mm |

---

## 8. Firmware Compatibility

- Target: ArduPilot Copter
- Boot: DFU via USB-C for firmware flashing
- Debug: SWD header (SWDIO, SWCLK, GND, 3.3V)

---

## 9. Design Constraints

- Hand-solderable components only (no BGA packages)
- All ICs available on DigiKey or Mouser
- IMU placed at geometric center of board
- SPI traces kept under 50mm, length-matched within 2mm
- Power and signal ground poured separately, joined at single star point

---

## 10. Out of Scope (future revisions)

- OSD chip
- Magnetometer
- Blackbox flash storage
- Redundant IMU