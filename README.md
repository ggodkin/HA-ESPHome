# ESP32 Greenhouse Controller

An ESP-IDF based greenhouse automation system designed for high-altitude environments. This system monitors battery health, internal chip temperature, and external environmental sensors via 1-Wire.

## Features
- **Dual-Rail Battery Monitoring:** 
  - **12V System (3S Li-ion):** High-power rail for fans/misters.
  - **1-Cell System (1S Li-ion):** Dedicated controller power monitoring.
- **Dynamic Update Intervals:** Adjust sensor polling frequency (1–60 min) via a Home Assistant slider.
- **Automated Safety Shutdown:** 
  - Kills high-power peripherals if the 12V rail drops below **11.1V**.
  - Kills high-power peripherals if the Controller rail drops below **3.8V**.
- **Power Management:** Background ADC sampling with a 1-minute sliding window average to prevent false triggers during motor inrush.

## Hardware Requirements
- **Microcontroller:** ESP32 (DevKit V1).
- **Voltage Dividers:**
  - **GPIO34 (12V Rail):** 100kΩ/22kΩ divider (Scale Factor: 5.545).
  - **GPIO35 (1-Cell Rail):** 10kΩ/10kΩ divider (Scale Factor: 2.0).
- **Sensors:** DS18B20 (1-Wire) on GPIO4 with 4.7kΩ pull-up.
- **Actuators:** Relays or MOSFETs on GPIO13 (Mister) and GPIO14 (Fan).

## Software Configuration
- **Framework:** ESP-IDF
- **Thresholds:**
  - 12V Critical: < 11.1V
  - 1S Critical: < 3.8V
