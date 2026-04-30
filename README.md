# STM32 Portable Proximity Warning System
 
A portable, battery-powered embedded proximity warning system built on the **STM32L476RG Nucleo-64**. Measures distance using an HC-SR04 ultrasonic sensor, displays live readings on a serial LCD, and drives a three-LED indicator array based on proximity zones.
 
---
 
## Demo
 
| Distance | LED State | LCD Status |
|---|---|---|
| > 30 cm | All OFF | Clear |
| 15 – 30 cm | 🟢 Green | Approaching |
| 5 – 15 cm | 🟢🟡 Green + Yellow | WARNING |
| ≤ 5 cm | 🟢🟡🔴 All ON | STOP! |
 
---
 
## Hardware
 
| Component | Model | Role |
|---|---|---|
| Microcontroller | STM32L476RG Nucleo-64 | Main processing unit |
| Ultrasonic sensor | HC-SR04 | Distance measurement |
| LCD display | Newhaven NHD-0420D3Z-FL-GBW | Distance + status output |
| Power supply module | MB-102 | 9V → 5V regulation |
| LEDs | Generic 5mm (green, yellow, red) | Proximity zone indicator |
| Enclosure | Custom SolidWorks design | 3D printed housing |
 
---
 
## Pin Configuration
 
| Signal | STM32 Pin | Mode |
|---|---|---|
| HC-SR04 TRIG | PA1 | GPIO Output |
| HC-SR04 ECHO | PA8 | TIM1 CH1 Input Capture |
| LCD RX (UART TX) | PA9 | USART1_TX |
| LED Green | PC0 | GPIO Output |
| LED Yellow | PC1 | GPIO Output |
| LED Red | PC2 | GPIO Output |
 
---
 
## Firmware Overview
 
Written in **bare-metal C** using the **STM32 HAL** library and configured with **STM32CubeMX**.
 
**Key implementation details:**
- HC-SR04 ECHO pulse measured via **TIM1 input capture interrupt** — captures rising and falling edges sequentially in `HAL_TIM_IC_CaptureCallback` to compute pulse width at 1µs resolution (prescaler = 79 @ 80MHz)
- Distance calculated as `pulse_width_us / 58`
- LCD driven over **USART1 at 9600 baud, 8N1** using the Newhaven RS-232 TTL command protocol
- Three-LED zone logic driven by GPIO outputs with 330Ω current limiting resistors
- Main loop execution time measured and displayed on LCD line 3 via `HAL_GetTick()`
- Standalone operation — powered via MB-102 module with JP5 set to E5V mode, no USB required
---
 
## Project Structure
 
```
├── Core/
│   ├── Src/
│   │   └── main.c          # All user firmware
│   └── Inc/
│       └── main.h          # Pin definitions and includes
├── *.ioc                   # STM32CubeMX configuration
└── README.md
```
 
---
 
## Building and Flashing
 
1. Open the project in **STM32CubeIDE**
2. Build with `Project → Build Project` (or `Ctrl+B`)
3. Flash with `Run → Debug` or `Run → Run` with the Nucleo connected via USB
4. Once flashed, disconnect USB and power via the MB-102 module — firmware runs standalone
---
 
