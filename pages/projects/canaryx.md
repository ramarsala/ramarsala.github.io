---
layout: page
title: Canary X Sensor
subtitle: Low-power embedded sensing platform commissioned by Lunar Outpost engineers
---

![Canary X embedded system mockup](../../../assets/img/projects/canary_fig1.png)

## Overview & Objectives

**Canary X** is an autonomous, low-power environmental sensing platform developed as a field session capstone commissioned and managed directly by engineers at **Lunar Outpost**. 

The primary objective was to engineer a robust microcontroller-based telemetry unit capable of gathering localized environmental metrics (air quality, temperature, humidity, pressure) alongside GPS positioning, and periodically transmitting these records to on-site servers under harsh and unpredictable field conditions. 

Our team delivered a complete end-to-end embedded hardware and firmware solution, achieving **99.9% operational uptime** through fault-tolerant state machine design. At the conclusion of the project, our team was awarded **1st Place in the Technical Presentation** evaluation by Lunar Outpost engineers and faculty judges.

---

## System Architecture & Inter-Device Communication

The system is coordinated by an Adafruit Feather microcontroller acting as the central processing and communications hub. The firmware manages a heterogeneous array of environmental sensors and communication peripherals:

- **Bus Communication (UART, SPI, I2C):** Implemented multi-bus drivers coordinating communications with environmental sensors, GPS modules, and external telemetry modems across I2C, SPI, and UART interfaces.
- **Efficient Sampling & Power Management:** Designed low-power polling routines that wake the MCU at defined sample intervals, interrogate sensor registers, verify data checksums, and transition the device into low-power sleep modes between transmission cycles.
- **Data Serialization:** Implemented a lightweight serialization format that encodes multi-sensor telemetry readings and timestamped GPS coordinates into compact, space-efficient JSON payloads optimized for constrained transmission channels.

---

## Resilient Multi-Network Priority Ladder & Dynamic Transmission

A critical challenge for remote sensor nodes is maintaining reliable connectivity when individual network interfaces degrade or experience intermittent outages. To guarantee continuous telemetry delivery, we architected a prioritized communication ladder:

1. **Ethernet (Primary):** Highest throughput and stability when wired infrastructure is accessible.
2. **Wi-Fi (Secondary):** Local wireless connection when Ethernet link carrier is unavailable.
3. **Cellular LTE-M / NB-IoT (Tertiary Fallback):** Wide-area cellular connection activated if both local wired and wireless networks fail.

### Fault-Tolerant State Machine & Data Retention

To govern this network ladder, we implemented a deterministic state machine:
- **Connection Health & Exponential Backoff:** The state machine monitors heartbeat responses and socket handshakes. If a connection drops, it executes intelligent retries with exponential backoff before escalating to the next available transport interface.
- **Persistent Flash Buffering:** If all network paths are temporarily offline, the firmware diverts telemetry into an on-board non-volatile flash storage buffer. Once connectivity is restored on any interface, cached records are dequeued and retransmitted chronologically without data loss.
- **Link Telemetry Logging:** Each packet encapsulates metadata detailing the active communication channel, signal strength, and retry counts, providing operators with actionable visibility into field network conditions.

---

## Embedded Cryptography & Security

Because the sensor nodes operate in unattended remote environments, data security and integrity were primary requirements:

- **Lightweight Cryptographic Payloads:** Wrapped network transports with resource-efficient cryptographic routines tailored for the microcontroller's memory and CPU footprint.
- **Integrity Validation & Anti-Tampering:** Every outgoing telemetry packet includes an encrypted hash / HMAC signature verified by the receiving server to detect payload tampering or transmission errors before database ingestion.

---

## Key Achievements

- **99.9% Telemetry Uptime:** Validated continuous operation and data persistence under simulated multi-network disconnect and power-cycle scenarios.
- **Drop-in Deployment:** Created a self-configuring, autonomous field node that requires zero on-site configuration—simply power up, and the node negotiates network access and begins reporting.
- **1st Place Award:** Recognized with first place in the final technical presentation for engineering rigor, firmware resilience, and end-to-end design quality.

---
