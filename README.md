# Room-Climate-Sensor

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
![ESPHome](https://img.shields.io/badge/ESPHome-Compatible-blue)

**Room-Climate-Sensor** is a system to monitor indoor temperature and humidity, displaying results on a 0.96" OLED screen. It uses a DHT22 sensor to send data to Home Assistant every 5 seconds. Built with ESPHome on an ESP32 NodeMCU-32S, it is powered by USB for continuous operation.

## Project Description

Maintaining optimal indoor climate is key for comfort. This project uses a DHT22 sensor to measure temperature and humidity, displaying readings on a 0.96" OLED screen and sending them to Home Assistant for remote monitoring or alerts. It runs continuously on USB power, updating every 5 seconds.

### Features
* Measures temperature and humidity with DHT22.
* Displays data on a 0.96" OLED screen (SSD1306).
* Sends data to Home Assistant every 5 seconds.
* Easy setup with ESPHome and OTA updates.
* Powered by USB for constant operation.

### Project Images
(Upload photos of the device, wiring, and OLED display to the images/ folder in this repository and update the links below.)

![Project Overview](images/project-overview.jpg)
![Wiring Diagram](images/wiring-diagram.jpg)
![OLED Display](images/oled-display.jpg)

(Placeholder for images. Add more images by including additional lines with the format ![Image Description](images/your-image.jpg) after uploading to the images/ folder.)

## Required Materials
* ESP32 NodeMCU-32S (~$5-10).
* DHT22 sensor (~$2-5).
* 0.96" OLED display (SSD1306, I2C) (~$3-5).
* Jumper wires.
* 4.7kΩ–10kΩ pull-up resistor for DHT22.
* Optional:
  * Breadboard for prototyping.
  * 5V 1A+ USB adapter for power.

## Prerequisites
* ESPHome: Install ESPHome (2025.2.2 or later) via Home Assistant add-on or standalone (see ESPHome Docs: https://esphome.io/).
* Home Assistant: Set up for integration (ESPHome add-on or dashboard required).
* Secrets File: Create secrets.yaml with WiFi and Home Assistant credentials.
* Arial Font: Place arial.ttf in your ESPHome config directory for OLED display.

## Installation
1. Set Up Secrets: Create secrets.yaml in your ESPHome config directory with:
   - wifi_ssid: YourWiFiNetwork
   - wifi_password: YourWiFiPassword
   - climate_sensor_api_encryption_key: your-home-assistant-api-key
   - climate_sensor_ota_password: your-ota-password

2. Get the Code: Clone this repository or copy climate-sensor.yaml to your ESPHome config directory.

3. Upload Firmware: Connect ESP32 via USB and run:
   - esphome compile climate-sensor.yaml
   - esphome upload climate-sensor.yaml

4. Check Logs: Monitor the device:
   - esphome logs climate-sensor.yaml
   - Expected logs (example, 14:00, Sep 20, 2025):
     * [14:00:10][D][wifi]: Connected to WiFi!
     * [14:00:12][D][api]: Connected to Home Assistant!
     * [14:00:15][D][dht:049]: Got Temperature=23.5°C Humidity=45.2% (every ~5s)
     * [14:00:15][D][sensor]: Temperature: 23.5 C sent
     * [14:00:15][D][sensor]: Humidity: 45.2 percent sent

5. Verify Home Assistant: In Home Assistant, go to Settings > Devices & Services > Integrations > ESPHome. Check climate-sensor device. Verify sensor.nodemcu_temperature and sensor.nodemcu_humidity in Developer Tools > States (updates every ~5s).

## Wiring Diagram

Connect the components as follows:

* **DHT22 Sensor**:
  - VCC to ESP32 3.3V (or 5V, check datasheet).
  - Data to ESP32 GPIO4.
  - GND to ESP32 GND.
  - Pull-up resistor (4.7kΩ–10kΩ) between Data and VCC.
  - NC pin: Leave unconnected.

* **0.96" OLED Display (SSD1306)**:
  - VCC to ESP32 3.3V.
  - GND to ESP32 GND.
  - SDA to ESP32 GPIO21.
  - SCL to ESP32 GPIO22.

* **Power**:
  - ESP32 VIN to 5V USB power (e.g., 5V 1A+ adapter).
  - ESP32 GND to USB GND.

**Schematic**:

```
DHT22                ESP32 (NodeMCU-32S)    OLED (SSD1306)       USB Power
+----------------+   +----------------+    +----------------+   +----------------+
| VCC            |-->| 3.3V           |    | VCC            |-->| 3.3V           |
| Data           |-->| GPIO4          |    | SDA            |-->| GPIO21         |
| GND            |-->| GND            |    | SCL            |-->| GPIO22         |
| NC             |   | (not connected)|    | GND            |-->| GND            |
+----------------+   |                |    +----------------+   |                |
(4.7kΩ–10kΩ resistor between Data and VCC)                    | VIN            |-->| USB 5V+        |
                                                             | GND            |-->| USB GND        |
                                                             +----------------+   +----------------+
```

## Power Consumption
* Power Consumption:
  - ESP32 (WiFi on): ~120 mA.
  - DHT22 (5s interval): ~1.2 mA.
  - OLED (SSD1306): ~10-20 mA.
  - Total: ~131.2-141.2 mA.
* Power Supply: Use a 5V 1A+ USB adapter for continuous operation.

## Usage
* Indoor Monitoring: Place in a room to track temperature and humidity, viewable on the OLED screen.
* Home Assistant Automation:
  * Alert for high humidity:
    - automation:
      - alias: Room Humidity Alert
      - trigger:
        - platform: numeric_state
        - entity_id: sensor.nodemcu_humidity
        - above: 60
      - action:
        - service: notify.mobile_app
        - data:
          - message: Room humidity too high: {{ states('sensor.nodemcu_humidity') }}%!
  * Log data for trends.
* Monitoring: Check Home Assistant or OLED screen for live readings (updated every ~5s).

## Contributing
* Submit pull requests for features (e.g., custom OLED layouts, additional sensors).
* Report bugs via GitHub Issues.
* Suggest feature improvements.

## License
This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments
* Built with ESPHome (https://esphome.io/) for Home Assistant integration.
* Inspired by DIY IoT projects for indoor climate monitoring.