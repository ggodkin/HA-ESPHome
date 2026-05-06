# HA-ESPHome Greenhouse Controller

A robust, low-power ESP32-based greenhouse automation system for Home Assistant using **ESP-IDF**. Optimized for solar/battery operation in Colorado.

## Features

- **Dynamic Update Interval** — Adjustable from 1 to 60 minutes via Home Assistant slider
- **Smart Battery Management** with hysteresis to prevent rapid cycling
- **Temperature Automation**:
  - Mister turns **ON** above 75°F, **OFF** below 73°F
  - Fan turns **ON** above 80°F, **OFF** below 78°F
- **Battery Priority** — Low battery conditions override temperature automation
- **All Temperatures in °F** — Greenhouse air + MCU core temperature
- **Power Optimizations** — WiFi power saving, fast connect, deep sleep support
- **Background ADC Sampling** — Stable battery readings with moving average filter

## Hardware Pinout

| Component                    | Pin      | Notes                              |
|-----------------------------|----------|------------------------------------|
| 1-Wire (DS18B20)            | GPIO4    | 4.7kΩ pull-up resistor required   |
| 12V System Voltage (ADC)    | GPIO34   | Voltage divider                    |
| Controller Battery (ADC)    | GPIO35   | Voltage divider                    |
| Greenhouse Mister           | GPIO13   | Relay or logic-level MOSFET        |
| Greenhouse Fan              | GPIO14   | Relay or logic-level MOSFET        |

## Configuration

All key parameters are located at the top of `esphome-greenhouse.yaml` under `substitutions`:

- Temperature thresholds (`mister_on_temp`, `fan_on_temp`, etc.)
- Battery voltage/percentage thresholds + hysteresis
- Voltage multipliers (calibration)
- Update interval, deep sleep duration, etc.

## Home Assistant Entities

**Sensors:**
- 12V System Voltage
- Controller Battery Capacity (%)
- Greenhouse Air Temperature (°F)
- Greenhouse Controller Internal Temp (°F)
- Greenhouse WiFi Signal
- Greenhouse Uptime
- And more...

**Switches:**
- Greenhouse Mister
- Greenhouse Fan

**Number:**
- Update Interval (minutes)

**Text Sensors:**
- Clock
- Chip Revision
- Detected Temperature Sensors

## Control Logic Priority

1. **Battery Protection** (Highest priority)
2. **Temperature Automation** (Only active when battery is healthy)

## Diagnostics

- Web server is available briefly on boot and when controller battery is healthy
- Check ESPHome logs for battery and temperature control messages
- Battery readings stabilize after ~60 seconds due to averaging filter

## Battery Reference (Controller)

| Battery % | Approximate Voltage | Action                     |
|-----------|---------------------|----------------------------|
| > 65%     | > 3.70V             | Normal operation           |
| < 60%     | ~3.65V              | Loads disabled             |
| < 30%     | ~3.40V              | Deep sleep (4 hours)       |

## Installation

1. Copy `esphome-greenhouse.yaml` and update your `secrets.yaml`
2. Flash the device:
   ```bash
   esphome run esphome-greenhouse.yaml
