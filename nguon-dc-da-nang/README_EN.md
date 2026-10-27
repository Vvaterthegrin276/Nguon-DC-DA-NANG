<div align="center">

# ⚡ Multi-Function DC Power Supply

*English summary — see the [Vietnamese README](README.md) for the full documentation*

![Version](https://img.shields.io/badge/version-5.2-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![Platform](https://img.shields.io/badge/platform-STM32F103C8T6-red)

</div>

## Overview

A DIY bench DC power supply built entirely from cheap, easy-to-find modules — designed for electronics beginners and students. It combines an **adjustable 0.8–22V / 8A power output**, a **voltmeter**, an **ammeter**, and a **mini oscilloscope** for AC ripple, all driven by an STM32F103C8T6 (Blue Pill) with a 2.4" TFT display.

All assembly instructions follow a "read the name, grab the part" style: every soldering step lists exactly which component goes to which pin, with a multimeter checkpoint after each step. Total cost is roughly **USD 30–40**.

## Key Features

| Feature | Specification |
|---|---|
| Adjustable DC output | 0.8 – 22V, up to 8A (XL4016 module + 10-turn potentiometers) |
| Voltage measurement | 0 – 30V via resistor divider on ADC PA0 |
| Current measurement | ±5A via ACS712-05B Hall sensor on ADC PA1 |
| Power display | V × I computed on the MCU |
| AC ripple scope | 2-stage LM358 amplifier module with adjustable gain, sampled on ADC PA2 |
| Display | 2.4" TFT ST7789V, 240×320, SPI |
| Controls | 4 push buttons: MODE / CHANNEL / TIME-DIV / HOLD |
| Auxiliary | 5V fan for heatsink cooling, LM2596 for logic supply |

## System Block Diagram

![System block diagram](images/so-do-khoi.png)

**Power flow:** 220V AC (switched socket + 2A fuse) → 24V/5A enclosed PSU → two branches:
1. **XL4016** step-down → adjustable output → ACS712 → output terminals
2. **LM2596** step-down → 5V logic rail → STM32, TFT, fan

**Measurement path:** output voltage (10k/1.2k divider) → PA0 • ACS712 output → PA1 • AC ripple (2-stage LM358 module, output pin 02) → PA2.

## Repository Structure

```
nguon-dc-da-nang/
├── README.md / README_EN.md   # This documentation
├── LICENSE                    # MIT License
├── docs/                      # Safety rules, BOM, pin table,
│                              # 14-step assembly guide, firmware
│                              # flashing guide, calibration
├── firmware/nguon_dc_da_nang/ # Arduino sketch (STM32duino core)
├── hardware/                  # Schematics & photos (work in progress)
└── images/                    # Diagrams used in the README
```

## Getting Started

1. **Buy the parts** — full BOM in [docs/02-danh-sach-linh-kien.md](docs/02-danh-sach-linh-kien.md) (Vietnamese)
2. **Read the safety rules** — [docs/01-an-toan.md](docs/01-an-toan.md) (mains voltage warning!)
3. **Solder the circuit** — 14 guided steps in [docs/04-huong-dan-lap-rap.md](docs/04-huong-dan-lap-rap.md)
4. **Flash the firmware** — Arduino IDE + STM32duino setup in [docs/05-huong-dan-nap-code.md](docs/05-huong-dan-nap-code.md)
5. **Calibrate** — [docs/06-hieu-chinh.md](docs/06-hieu-chinh.md)

## Pin Map (summary)

| Pin | Connected to | Function |
|---|---|---|
| PA0 | 10kΩ / 1.2kΩ divider | ADC — voltage sense |
| PA1 | ACS712 via 10k/20k divider | ADC — current sense |
| PA2 | 2-stage LM358 module (output pin 02) | ADC — AC ripple |
| PA3 / PB10 / PB11 / PB12 | Push buttons (to GND) | MODE / CHANNEL / TIME-DIV / HOLD |
| PA4 / PA5 / PA6 / PA7 / PB0 | TFT | CS / SCK / DC / MOSI / RST |

Full table with component values: [docs/03-bang-chan-stm32.md](docs/03-bang-chan-stm32.md)

## Roadmap

- [x] v5.1 — Perfboard module build
- [x] v5.2 — Replaced discrete LM358 stage with a 2-stage LM358 amplifier module (current)
- [ ] v6.0 — Custom PCB design
- [ ] v6.1 — Encoder-driven menu for V/I setting
- [ ] v6.2 — USB serial data streaming to PC
- [ ] v7.0 — 3D-printed enclosure (public STL)

## License

Released under the [MIT License](LICENSE) — free to use, modify and share, including commercially, as long as the copyright notice is kept.
