# Flight Controller PCB — Requirements Document
**Project:** Custom STM32-based Drone Flight Controller  
**Designer:** Desmond Bolia  
**Revision:** v0.1  
**Date:** April 11, 2026  
**Reference Design:** Pixhawk 6C (Holybro) — adapted for STM32F405

---

## 1. Mission Context

This board is designed for autonomous quadcopter operation in GPS-available
and GPS-degraded environments. It is derived from my senior capstone
project — a fiber-optic-tethered autonomous UAV with onboard AI inference and
MAVLink telemetry — and targets ArduPilot firmware compatibility.

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
| MCU | STM32F405RGT6 | ArduPilot native, LQFP64 hand-solderable |
| Core | ARM Cortex-M4 @ 168MHz | Sufficient for flight control loop |
| Flash | 1MB | Fits ArduPilot firmware |
| Package | LQFP64 | Hand-solderable, widely available |

Peripherals verified against STM32F405RGT6 datasheet DS8626 Rev 11:
4x USART, 2x UART, 3x SPI, 3x I2C, 1x USB OTG FS.

Note: Pixhawk 6C uses STM32H743. STM32F405 selected for reduced complexity
on first PCB design iteration with full ArduPilot support.

---

## 4. Sensors

| Sensor | Interface | Function |
|---|---|---|
| ICM-42688-P | SPI1 | Primary IMU (accel + gyro) |
| BMP388 | I2C1 | Barometric altimeter |

---

## 5. Communication Interfaces

| Interface | Peripheral | Connector | Function |
|---|---|---|---|
| SPI1 | ICM-42688-P | — | IMU data |
| USART1 | GPS | JST-GH 6-pin | GPS module |
| USART2 | Telemetry | JST-GH 6-pin | MAVLink / GCS |
| USART3 | Debug | 4-pin header | Firmware debug |
| I2C1 | BMP388 | — | Barometer |
| USB OTG-FS | — | USB-C | Firmware flashing, config |

Remaining peripherals (USART6, UART4, UART5) reserved for future revisions.

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
- ICM-42688-P requires stencil and solder paste (LGA package)
- All ICs available on DigiKey or Mouser
- IMU placed at geometric center of board
- SPI traces kept under 50mm, length-matched within 2mm
- Power and signal grounds poured separately, joined at single star point

---

## 10. Out of Scope (future revisions)

- OSD chip
- Magnetometer
- Blackbox flash storage
- Redundant IMU
- USART6, UART4, UART5 breakouts