# De Dietrich Heat Pump BLE → Home Assistant

ESP32 + ESPHome bridge for a De Dietrich/BDR Thermea heat pump using [esphome-bdr-ble](https://github.com/d-lebed/esphome-bdr-ble). No cloud or proprietary gateway required. Tested with ESPHome 2026.11.0.

## Setup

1. Create your credentials file:
   ```bash
   cp secrets.yaml.example secrets.yaml
   ```
   Edit `secrets.yaml` with your Wi-Fi details, heat-pump BLE MAC and pairing PIN.

2. First flash over USB:
   ```bash
   esphome run dedietrich-ble.yaml
   ```

3. Later updates over Wi-Fi:
   ```bash
   esphome upload dedietrich-ble.yaml
   ```

4. In Home Assistant: **Settings → Devices & Services → ESPHome → Add** and select **De Dietrich Heat Pump**. Reload the integration if entities still show `unknown`.

## Usage

- Readable entities include `Operating Status Text`, `Operating sub Status Text`, `Error Code` (`A/H/E xx.xx`), `Error Severity`, `Error Details`, `Three way valve status` (`CH`/`DHW`/`None`) and `DHW Activity` (`Off`/`Eco`/`Comfort`/`Thermal disinfection`).
- **Low Noise Start Time** and **Low Noise Stop Time** are normal time pickers with 10-minute resolution.
- **DHW Thermal Disinfection** is the manual's `DP004` / **Legionella calor.** parameter.
- The heat pump accepts one BLE connection. Close the De Dietrich phone app or disable **Heat Pump BLE Connect** before using the app.

## Troubleshooting

Check `BLE Status` in Home Assistant first.

Logs over Wi-Fi:
```bash
esphome logs dedietrich-ble.yaml --device dedietrich-ble.local
```

Logs over USB:
```bash
esphome logs dedietrich-ble.yaml --device /dev/cu.usbserial-XXXX
```

Set a DHCP reservation for the ESP32 to avoid its IP address changing.
