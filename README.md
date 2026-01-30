# FTMS Bike Trainer Console

A minimal browser application for connecting to Bluetooth FTMS (Fitness Machine Service) compatible bike trainers and Zwift Click virtual shifting controllers.

## Features

- Connect to FTMS-compatible smart trainers (e.g., Van Rysel D100)
- Display real-time metrics: power, cadence, speed, resistance
- Manual resistance control (0-100%)
- Grade/slope simulation (-10% to +15%)
- Virtual gear shifting (1-24 gears)
- Zwift Click controller support (experimental)
- Keyboard shortcuts (Arrow Up/Down or +/- for gear shifting)

## Usage

1. Open `index.html` in a supported browser (Chrome, Edge on desktop)
2. Click "Connect Trainer" to pair with your FTMS trainer
3. Optionally click "Connect Zwift Click" to pair shift controllers
4. Use the UI controls or keyboard to adjust resistance/gears

## Browser Requirements

- **Chrome/Edge** on Windows, macOS, Linux, or Android
- Web Bluetooth API must be enabled
- May need to enable `chrome://flags/#enable-web-bluetooth` on some platforms
- **iOS Safari does NOT support Web Bluetooth**

## Van Rysel D100 Notes

- Supports only Bluetooth (no ANT+)
- Single Bluetooth connection at a time
- Max power: 600W
- Max incline simulation: 6%
- No built-in cadence sensor

## FTMS Protocol Reference

### Service UUID
- **FTMS Service:** `0x1826`

### Characteristics

| Characteristic | UUID | Operation |
|----------------|------|-----------|
| Fitness Machine Feature | `0x2ACC` | READ |
| Indoor Bike Data | `0x2AD2` | NOTIFY |
| Supported Resistance Range | `0x2AD6` | READ |
| Control Point | `0x2AD9` | WRITE/INDICATE |
| Machine Status | `0x2ADA` | NOTIFY |

### Control Point Opcodes

| Opcode | Function | Parameters |
|--------|----------|------------|
| `0x00` | Request Control | - |
| `0x01` | Reset | - |
| `0x04` | Set Target Resistance | uint8 (0-100, resolution 0.1) |
| `0x05` | Set Target Power | int16 (watts) |
| `0x07` | Start/Resume | - |
| `0x08` | Stop/Pause | uint8 (1=stop, 2=pause) |
| `0x11` | Set Simulation | wind, grade, crr, cw |

### Indoor Bike Data Format

```
Byte 0-1: Flags (16-bit little-endian)
  Bit 0: 0 = Instantaneous Speed present (inverted logic)
  Bit 1: Average Speed present
  Bit 2: Instantaneous Cadence present
  Bit 3: Average Cadence present
  Bit 4: Total Distance present
  Bit 5: Resistance Level present
  Bit 6: Instantaneous Power present
  Bit 7: Average Power present

Data fields follow in order based on flags:
- Speed: uint16, resolution 0.01 km/h
- Cadence: uint16, resolution 0.5 rpm
- Distance: uint24, resolution 1 m
- Resistance: int16
- Power: int16, resolution 1 W
```

## Zwift Click Protocol Reference

### Service UUID
- **Legacy:** `00000001-19ca-4651-86e5-fa29dcdd09d1`
- **New (2025+):** `0xFC82`

### Characteristics

| Characteristic | UUID | Operation |
|----------------|------|-----------|
| ASYNC | `00000002-19ca-4651-86e5-fa29dcdd09d1` | Notify |
| SYNC_RX | `00000003-19ca-4651-86e5-fa29dcdd09d1` | Write |
| SYNC_TX | `00000004-19ca-4651-86e5-fa29dcdd09d1` | Indicate |

### Handshake
Send ASCII "RideOn" (`52 69 64 65 4f 6e`) to SYNC_RX, expect "RideOn" response on SYNC_TX.

After handshake, communication uses Protocol Buffer messages (message ID `0x23` for button presses).

## Sources and References

### Official Specifications
- [Bluetooth FTMS Specification](https://www.bluetooth.com/specifications/specs/fitness-machine-service-1-0/)
- [FTMS v1.0 PDF](https://www.onelap.cn/pdf/FTMS_v1.0.pdf)

### Zwift Protocol Reverse Engineering
- [Makinolo - Zwift Ride Protocol](https://www.makinolo.com/blog/2024/07/26/zwift-ride-protocol/)
- [Makinolo - Zwift Trainer Protocol](https://www.makinolo.com/blog/2024/10/20/zwift-trainer-protocol/)
- [zwiftplay GitHub](https://github.com/ajchellew/zwiftplay)
- [SHIFTR Project](https://github.com/JuergenLeber/SHIFTR)

### Web Bluetooth Examples
- [Google Web Bluetooth Samples](https://googlechrome.github.io/samples/web-bluetooth/)
- [web-bluetooth-bike-trainer](https://github.com/wklenk/web-bluetooth-bike-trainer)
- [Auuki Indoor Cycling App](https://github.com/dvmarinoff/Auuki)
- [Decathlon FTMS Treadmill Console](https://github.com/Decathlon/domyos-developers/tree/main/ftms-treadmill-web-console)

### Van Rysel D100 Reviews
- [DC Rainmaker - Van Rysel D100 Review](https://www.dcrainmaker.com/2025/02/decathlon-van-rysel-d100-smart-trainer-review.html)
- [BikeRadar - Van Rysel D100 Review](https://www.bikeradar.com/reviews/training/indoor-trainers/van-rysel-d100-home-trainer-with-zwift-cog-and-click-review)

### Additional Resources
- [Nordic DevZone - FTMS Indoor Bike Data](https://devzone.nordicsemi.com/f/nordic-q-a/56157/ble--nrfconnect-ftms-profile-indoor-bike-data-characteristic)
- [pycycling FTMS Documentation](https://zacharybull.com/pycycling/pycycling.fitness_machine_service.html)
- [ble-ftms Arduino Library](https://github.com/kswiorek/ble-ftms)
- [Zwack BLE Simulator](https://github.com/paixaop/zwack)

## License

MIT
