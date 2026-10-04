# dspic33-bma280-uart

Firmware for a **dsPIC33EP512MU810** that reads a **BMA280** accelerometer over SPI, computes roll/pitch, and streams data over UART. Built with MPLAB X and the XC16 compiler.

The firmware targets a board paired with the manufacturer's add-on module (four motors with wheels), with the goal of letting the robot move through its surrounding space while autonomously avoiding obstacles. The code in this repository covers the sensing and communication layer: accelerometer acquisition, orientation estimation and the UART interface.

## Features

- 100 Hz main loop paced by Timer1 (10 ms period), with deadline-miss detection
- Accelerometer sampling at 50 Hz over SPI1
- Roll and pitch angle computation from raw 12-bit axis readings
- UART1 interrupt-driven RX with circular buffer and command parser
- Configurable accelerometer bandwidth and output rate at runtime

## UART protocol

Commands received:

| Command   | Description                                   |
|-----------|-----------------------------------------------|
| `$BW,xx*` | Set accelerometer bandwidth code (8..15)      |
| `$HZ,yy*` | Set `$ACC` output rate: 0, 1, 2, 5 or 10 Hz   |

Messages transmitted:

| Message        | Description                                       |
|----------------|---------------------------------------------------|
| `$ACC,X,Y,Z*`  | Raw signed 12-bit readings, at the selected rate  |
| `$ANG,R,P*`    | Roll and pitch in degrees, fixed at 5 Hz          |

## Project layout

| File              | Purpose                                         |
|-------------------|-------------------------------------------------|
| `main.c`          | Main loop, scheduling, UART output              |
| `acc.c` / `acc.h` | BMA280 SPI driver and angle computation         |
| `uart.c` / `uart.h` | UART1 driver and command parser               |
| `timer.c` / `timer.h` | Timer-based periods and delays              |
| `nbproject/`      | MPLAB X project configuration                   |

## Build

1. Install [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide) and the XC16 compiler (v2.10 used).
2. Open this folder as a project in MPLAB X.
3. Build and program the dsPIC33EP512MU810 target.

The `.mc3` file is the MPLAB Code Configurator configuration.
