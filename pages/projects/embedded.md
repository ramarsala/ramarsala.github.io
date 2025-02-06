---
layout: page
title: Embedded Programming
subtitle: Baremetal programs directly interfacing with hardware
---
Projects include Canary X, autonomous robot, BPM calculator, and others.

The Canary X project was a field session project provided by Lunar Outpost. The primary goal was designing a series of embedded systems to collect sensor information which would be sent to an onsite server using a json payload.

The sensor information included localization provided by a GPS sensor, and various environmental information such as temperature. The information was requested and retrieved from various sources by an adafruit feather through UART, SPI, and I2C. A process was developed to package the information from into a json format using open source libraries.

The system was required to transmit the information through various encrypted channels, including ethernet, wifi, and cellular methods. A state machine was designed to continuously prioritize the methods from lowest to highest complexity and energy cost. Various libraries and custom firmware was developed to encrypt communications through the channels.

The project was presented along with the other teams in field sessions. The presentation won 1st place in technical presentation by class vote. Overall the project was completed successfully.

Pictures to be added.

Autonomous robot to be added.

