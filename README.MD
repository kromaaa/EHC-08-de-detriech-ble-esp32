# De Dietrich Heat Pump BLE → Home Assistant (ESPHome)

Local Bluetooth Low Energy (BLE) bridge connecting a **De Dietrich / BDR Thermea heat pump** (`EHC-08` + `GTW-Bluetooth` + `MK3`) directly to **Home Assistant** using an **ESP32** and [esphome-bdr-ble](https://github.com).

No cloud connection or extra proprietary gateways (such as Smart TC° or GTW-08 Modbus) are required.

---

## Hardware & Requirements

* **Heat Pump:** De Dietrich with `EHC-08` control board and built-in `GTW-Bluetooth` module
* **Microcontroller:** Any standard **ESP32** dev board (`esp32dev`, placed within Bluetooth range of the heat pump)
* **Credentials needed:**
  * Heat pump BLE MAC address (e.g. `00:A0:50:xx:xx:xx`)
  * 6-digit Bluetooth pairing PIN (printed on the `GTW-Bluetooth` module sticker / shown on the MK3 display during pairing)
  * Local 2.4 GHz Wi-Fi SSID & password

> **Note:** The `GTW-Bluetooth` module only accepts **one active BLE connection at a time**. Keep Bluetooth disabled on phones running the *De Dietrich Pro* app while the ESP32 is connected, or toggle the **Heat Pump BLE Connect** switch off in Home Assistant whenever you want to use the mobile app.

---

## Quick Start

### 1. Create `secrets.yaml`
Copy `secrets.yaml.example` to `secrets.yaml` (which is ignored by Git) and fill in your credentials:

```bash
cp secrets.yaml.example secrets.yaml
```

```yaml
wifi_ssid: "YOUR_WIFI_SSID"
wifi_password: "YOUR_WIFI_PASSWORD"
ble_pin: "YOUR_PIN"
ble_mac: "HEAT_PUMP_MAC-e.g.00:A0:50:00:00:00"
```

### 2. Install ESPHome CLI
```bash
pip install esphome
```

### 3. Validate & Flash Over USB (First Time)
Plug the ESP32 into your computer via USB and run:

```bash
esphome config dedietrich-ble.yaml
esphome run dedietrich-ble.yaml
```

### 4. Subsequent Updates Over Wi-Fi (OTA)
Once the ESP32 is online near the heat pump, push configuration changes wirelessly:

```bash
esphome run dedietrich-ble.yaml --device dedietrich-ble.local
```

### 5. Add to Home Assistant
1. Open Home Assistant → **Settings → Devices & services**.
2. Add the auto-discovered **De Dietrich Heat Pump** ESPHome device (or add manually using the ESP32's IP address).

---

## Exposed Entities

### Monitoring (`sensor` & `binary_sensor`)
* **Temperatures & Hydraulics:** Outside Temperature (`AM027`), Flow Temperature (`AM016`), DHW Temperature (`DM001`), Water Pressure (`AM019`)
* **Status & Inputs:** Operating Status Code (`AM012`), Silent Mode Active (`AM002`), BL1/BL2 Contact Positions (`HM004`, `HM005`), Smart Grid Current Mode (`AM657`), Backup Heater Stage 1 & 2 (`HM012`, `HM013`)
* **Energy Counters:** CH / DHW / Backup / Total Consumption & Delivery (`AC005`–`AC009`, `AC019`, `AC026`, `varApTotalEnergyDelivery`)
* **Fault & Blocking Tracking:** HMI-style error code, severity and details (`0x5047:01/:03/:07`) via `Error Code`, `Error Severity`, `Error Details`

### Controls (`number`, `select` & `datetime`)
* **Domestic Hot Water (DHW):** Comfort Setpoint (`DP070`), Operating Mode (`DP200`), Legionella calor. / Thermal Disinfection (`DP004`)
* **Central Heating (CH):** Room Comfort / Temporary / Manual / Holiday Setpoints (`CP080`, `CP510`, `CP200`, `CP060`), Flow Setpoint & Min/Max Limits (`CP010`, `CP260`, `CP000`), Heating Curve Comfort/Reduced Base & Slope (`CP210`, `CP220`, `CP230`), Zone Mode (`CP320`), Time Program (`CP570`)
* **Heat Pump Limits:** Bivalent Temperature (`HP000`), Max HP Flow Temperature (`HP002`)
* **Low Noise / Silent Mode:** Silent Mode Level (`HP058`), Start & Stop Time (`HP094`, `HP095`) as normal time pickers; the device stores 10-minute slots internally, Max Compressor & Fan Speed (`HP168`, `HP169`)
* **BL1 / BL2 & Smart Grid / PV Boost:** BL1/BL2 Functions & Contact Logic (`AP001`, `AP098`, `AP100`, `AP099`), PV Self-Consumption Mode & Contact Logic (`AP055`, `AP057`), PV/Smart Grid DHW & Heating Offsets (`HP092`, `DP512`, `HP091`, `HP213`)
