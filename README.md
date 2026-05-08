# HA-ESPHome Greenhouse Controller

A robust, low-power ESP32-based greenhouse automation system for Home Assistant using **ESP-IDF**. Optimized for reliable solar/battery operation in Colorado.

## Features

- **Fully Autonomous Operation** — All automation continues to run even without WiFi or Home Assistant
- **Dynamic Update Interval** — Adjustable from 1 to 60 minutes via Home Assistant
- **Smart Battery Management** with hysteresis
- **Temperature Automation**:
  - Mister turns **ON** above 75°F, **OFF** below 73°F
  - Fan turns **ON** above 80°F, **OFF** below 78°F
- **Battery Priority** — Low battery conditions override temperature controls
- **All Temperatures in °F** — Greenhouse air + MCU core temperature
- **WiFi Fallback** — Automatic Access Point if main WiFi fails
- **Security Focused** — OTA button hidden from web interface

## Hardware Pinout

| Component                    | Pin      | Notes                              |
|-----------------------------|----------|------------------------------------|
| 1-Wire (DS18B20)            | GPIO4    | 4.7kΩ pull-up resistor required   |
| 12V System Voltage (ADC)    | GPIO34   | Voltage divider                    |
| Controller Battery (ADC)    | GPIO35   | Voltage divider                    |
| Greenhouse Mister           | GPIO13   | Relay or logic-level MOSFET        |
| Greenhouse Fan              | GPIO14   | Relay or logic-level MOSFET        |

## Offline / Autonomous Operation

The controller operates **independently** of WiFi and Home Assistant:

- Battery protection and temperature automation (Mister & Fan) continue to function normally if WiFi or HA is unavailable.
- If the main WiFi network is down, the device automatically creates a fallback Access Point named **`Greenhouse-ESP32`** (password: `greenhouse123`).

## Configuration

All important settings are located at the top of `esphome-greenhouse.yaml` under `substitutions`:

- Temperature thresholds (`mister_on_temp`, `fan_on_temp`, etc.)
- Battery thresholds and hysteresis values
- Voltage calibration multipliers
- Update interval, deep sleep duration, etc.

## Home Assistant Entities

**Sensors:**
- 12V System Voltage
- Controller Battery Capacity (%)
- Greenhouse Air Temperature (°F)
- Greenhouse Controller Internal Temp (°F)
- Greenhouse WiFi Signal
- Greenhouse Uptime

**Switches:**
- Greenhouse Mister
- Greenhouse Fan

**Number:**
- Update Interval (minutes)

**Text Sensors:**
- Clock
- Chip Revision
- **Greenhouse Temp Sensor Address** (shows actual 1-Wire address)

## Control Logic Priority

1. **Battery Protection** (Highest priority)
2. **Temperature Automation** (Only runs when battery allows)

## Diagnostics

- Web server is available briefly on boot and when controller battery is healthy
- **OTA updates are disabled in the web UI** for improved security (use ESPHome CLI or HA to update)
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

##TO DO
- Add pull up resistors for fan and mist MCU outputs, potentially pull down for MOSFET
- Adjust 12v divider - add filter
