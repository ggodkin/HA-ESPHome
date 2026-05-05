# ESP32 Greenhouse Controller

An ESP-IDF based greenhouse automation system designed for high-altitude environments[cite: 2, 3]. This system monitors battery health, internal chip temperature, and environmental sensors via 1-Wire.

## Features
- **Dual-Rail Battery Monitoring:** 
  - **12V System:** Monitors for motor/fan rails with hysteresis to prevent relay chatter[cite: 1, 2].
  - **Controller Rail:** Uses custom voltage-to-capacity mapping for precision power management[cite: 1, 2].
- **Adaptive Power Management:**
  - **Normal Mode:** Continuous operation with UI-defined update intervals (1–60 min)[cite: 1, 2].
  - **Deep Sleep Mode:** Triggers when controller battery capacity drops below **60% (3.27V)** to conserve power between cycles[cite: 1].
  - **Emergency Shutdown:** Kills all high-power peripherals if capacity drops below **30% (3.22V)**[cite: 1, 2].
- **Dynamic Update Intervals:** Adjust sensor polling frequency via Home Assistant slider[cite: 1].
- **Signal Stability:** Background ADC sampling with 1-minute sliding window moving averages[cite: 1, 2].
- **Adaptive Power Management:** Continuous operation or Deep Sleep based on capacity thresholds[cite: 1, 2].
- **Logging Diagnostics:** Real-time logging of battery voltage used for hysteresis and sleep logic[cite: 1].

## Diagnostics & Troubleshooting
- **Web UI Accessibility:** The Web UI is only available during the `on_boot` delay (15s) and while the battery is above 60% (3.27V)[cite: 1, 2].
- **Filter Warming:** The ADC uses a 6-sample sliding window. It takes 60 seconds of uptime for the battery readings to stabilize. Logic triggers may be delayed until the window is full[cite: 1].
- **Serial/Web Logs:** Look for `Battery Logic Check: Voltage is X.XXXV` to see the exact float value being compared against the thresholds[cite: 1].

## Hardware Configuration
- **Framework:** ESP-IDF (Strict)[cite: 1, 3].
- **12V Rail (GPIO34):** 100kΩ/22kΩ divider[cite: 2].
- **1S Rail (GPIO35):** 10kΩ/10kΩ divider[cite: 2].
- **Actuators:** Mister (GPIO13), Fan (GPIO14)[cite: 2].

## Battery Capacity Reference (1-Cell)
| Capacity | Voltage | Action |
| :--- | :--- | :--- |
| 100% | 4.20V | Normal Operation |
| 60% | 3.27V | **Enter Deep Sleep** |
| 30% | 3.22V | **Emergency Shutdown** |
