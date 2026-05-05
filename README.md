# ESP32 Greenhouse Controller (ESP-IDF)

A sophisticated greenhouse automation and monitoring system built on the **ESP-IDF** framework via ESPHome. This controller is optimized for solar-powered operation in Colorado, featuring noise-filtered power monitoring and dynamic sensor polling.

## 🚀 Key Features

*   **Dynamic Polling Interval:** Controlled via a Home Assistant slider (1–60 min). Updates occur instantly when the slider is moved.
*   **Dual-Layer Power Monitoring:** 
    *   **Background Sampling:** ADCs sample every 10s to maintain a 1-minute sliding window average.
    *   **Snapshot Publishing:** Public sensors only update at the user-defined interval to reduce log noise and network overhead.
*   **Safety Interlocks:** Automated critical shutdown of high-power peripherals (Fans/Misters) based on battery voltage thresholds.
*   **WiFi Optimization:** Uses `fast_connect` and optimized output power to reduce radio "on-time" and prevent voltage brownouts.
*   **ESP-IDF Framework:** Leverages the native ESP32 framework for better stability and advanced memory management.

## 🛠 Hardware Configuration

| Component | ESP32 Pin | Description |
| :--- | :--- | :--- |
| **1-Wire Bus** | GPIO4 | DS18B20 Sensors (Requires 4.7kΩ pull-up) |
| **12V ADC** | GPIO34 | System Battery (via 100kΩ/22kΩ divider) |
| **Ctrl ADC** | GPIO35 | Internal Battery (via voltage divider) |
| **Mister** | GPIO13 | Relay/MOSFET Output |
| **Fan** | GPIO14 | Relay/MOSFET Output |

## 📊 Sensor Logic

The system utilizes a "Template" sensor pattern to decouple physical hardware reading from data reporting:
1. **Raw Sensors:** Run at `10s` intervals (internal only) to fill the `sliding_window_moving_average`.
2. **Template Sensors:** Updated via a background `while` loop script triggered by the `temp_interval` number entity.
3. **Internal Health:** Monitors ESP32 chip temperature to detect enclosure overheating.

## 📦 Setup & Deployment

### Prerequisites
*   ESPHome (Tested on **2025.7.0+**)
*   A valid `secrets.yaml` containing WiFi and API credentials.

### Installation
```bash
# Clone the repository
git clone <your-repo-url>
cd greenhouse-controller

# Compile and upload
esphome run esphome-web-abcdd4.yaml# ESP32 Greenhouse Controller

## Diagnostics & Troubleshooting
- **Web UI Accessibility:** The Web UI is only available during the `on_boot` delay (15s) and while the battery is above 60% (3.27V)[cite: 1, 2].
- **Filter Warming:** The ADC uses a 6-sample sliding window. It takes 60 seconds of uptime for the battery readings to stabilize. Logic triggers may be delayed until the window is full[cite: 1].
- **Serial/Web Logs:** Look for `Battery Logic Check: Voltage is X.XXXV` to see the exact float value being compared against the thresholds[cite: 1].

## Battery Capacity Reference (1-Cell)
| Capacity | Voltage | Action |
| :--- | :--- | :--- |
| 100% | 4.20V | Normal Operation |
| 60% | 3.27V | **Enter Deep Sleep** |
| 30% | 3.22V | **Emergency Shutdown** |
