---
layout: page
title: Embedded Projects
subtitle: Baremetal projects in robotics, signal processing, control, and FPGA/SoC
---
This page collects various embedded projects that I built, primarily in team environments. I learned many things about working with embedded systems, programming in low level languages, and working in a collaborative environment with other team members.

---

## Canary X Field Session Project

![Canary X embedded system mockup](../../../assets/img/projects/canary_fig1.png)

Canary X is a low power sensing platform that aggregates GPS and environmental data and forwards it to an on site server. Our team designed the end to end flow: an Adafruit Feather acts as the coordinator and talks to sensors over UART, SPI, and I2C; we added a compact serialization layer so readings become small JSON messages that are easy to store and replay. Communication is organized as a priority ladder: try ethernet first, then wifi, then a cellular link if both local paths fail. A shared state machine keeps links healthy, retries with backoff, and logs which path was used so we can measure link quality in the field.

Security mattered because the device might sit unattended. We wrapped the transports with light weight crypto libraries that fit the MCU footprint and validated message integrity on the server. The result was a drop in node that could be powered up, find at least one path to the server, and report location and environmental data without operator attention. The project was presented as part of the field session and our team placed first in the technical presentation vote.

---

## Autonomous Robot - Fiducial Navigation and Closed loop Drive

![Autonomous robot navigating between fiducial markers](../../../assets/img/projects/seed_fig1.png)

This robot navigates toward visual fiducial markers placed on the floor. As a team we split perception, control, and hardware. A small camera detects each marker and estimates relative pose using the marker corners, which gives x, y, and heading with enough accuracy to steer. The chassis uses a differential drive with encoders on both wheels. We run a heading controller that computes the error to the desired bearing and turns it into differential PWM values, with encoder feedback closing the loop so the robot keeps a steady speed even when one wheel drags or the battery sags.

Losing sight of a marker is common in tight turns, so we added a recovery behavior that starts a small spiral search and caps angular velocity to avoid tipping or wheel slip. With these pieces in place the robot approaches each marker smoothly and stops inside a small acceptance radius. The stack stays intentionally simple: vision for coarse pose, encoders for short term stabilization, and a PID that prefers smooth corrections over aggressive turns.

---

## Hardware BPM Detector - Analog Envelope plus Digital Timing

![Breadboard BPM detector with analog envelope and seven segment readout](../../../assets/img/projects/bpm_fig1.jpg)

The BPM detector listens to a music source and produces a stable beats per minute readout on four seven segment displays. We built an analog front end that amplifies audio, applies a band pass to favor percussive content, rectifies it, and then smooths it with an RC low pass to create an envelope. A Schmitt trigger comparator turns that envelope into clean digital pulses, which is essential because the digital logic only works when pulses are consistent.

On the digital side we measure the time between rising edges and convert that interval into BPM. Short term intervals are noisy, so we reject outliers and combine the last few intervals with a sliding median that damps jitter without making the display feel sluggish. The display is multiplexed at about 1 kHz so the digits look steady without high current draw. The comparator threshold and envelope time constant are adjustable, which lets the device lock on quickly across different genres.

---

## LabVIEW Vending Machine Simulation

![LabVIEW vending machine simulation using NI hardware in the loop](../../../assets/img/projects/labview_fig1.png)

This project models a small vending machine and tests the control logic with National Instruments hardware in the loop. The front panel lets a user select a product, insert credit, and see change returned. Behind the panel we implemented a queued state machine that cycles through Idle, Credit, Vend, Change, and Fault. The logic debounces user input, enforces timeouts, and guards against issues like double presses or stuck sensors. When an NI myDAQ is attached we can trigger vend events from real inputs and watch the state transitions in real time.

What made this useful was the test harness. We recorded input sequences that cover normal purchase, underpayment, out of stock, and error recovery, and replayed them to confirm that the state machine returns to a safe Idle state every time. Though small, the system captures patterns that scale to larger industrial controls: explicit states, input sanitation, and reproducible tests.

---

## FPGA LED Bounce Game in Verilog (DE2 115)

![DE2 115 FPGA board with LED bounce reflex game](../../../assets/img/projects/verilog_fig1.png)

The LED bounce game is a reflex challenge that runs entirely in hardware on a Cyclone IV based DE2 115 board. A single LED scans across a row at a human friendly rate produced by a clock divider. When the lit LED reaches either end, the player must press a button inside a small window to bounce it back. A miss ends the run and the seven segment display shows the final score. We implemented the logic as a small finite state machine with MoveLeft, MoveRight, Bounce, and Fail states. Button input is synchronized and debounced so it behaves well at the system clock rate.

The project demonstrates clean synchronous design. All state transitions are edge triggered, the scan direction changes only in the Bounce state, and the scoring path is separated from the motion path so timing closure is straightforward. Because the design is parametric we can change scan width and speed without touching the core logic, which made classroom demos easy.

---
