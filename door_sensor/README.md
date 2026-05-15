# homesensors / hardware / door_sensor

[![License: CERN-OHL-P-2.0](https://img.shields.io/badge/License-CERN--OHL--P--2.0-blue.svg)](../LICENSE)

KiCad project for the `homesensors` BLE door sensor — a custom small
PCB with STM32WB09KEVx + TCS40DPR Hall sensor + active-low LED + CR2032
holder. Part of the [`homesensors`](https://github.com/homesensors/sensorkit)
DIY smart-home sensor kit, and the first fabricated board in the
[hardware repo's](../README.md) board family.

![Side A — KiCad render](https://raw.githubusercontent.com/homesensors/sensorkit/main/docs/images/ble_door_sensor_sideA.png)
![Side B — KiCad render](https://raw.githubusercontent.com/homesensors/sensorkit/main/docs/images/ble_door_sensor_sideB.png)

## Revisions

| Tag | Status | What changed |
|---|---|---|
| `door_sensor-rev-a` | **Fabricated** | Initial fab. Known issues: missing VDDA_VCAP 0.22 µF decoupling cap; no external battery-monitor divider. Both worked around by hand-rework on the prototype unit. |
| `door_sensor-rev-b` | **At fab** | Folds in the VDDA_VCAP cap and the external battery-monitor divider (1 MΩ / 1 MΩ + 1 µF X7R, midpoint into PB1 = ADC1_VINP1) + JST-MX 4P expansion connector on PA8–PA11. |

The older tag names `pcb-rev-a` / `pcb-rev-b` still exist as aliases
pointing at the same commits — those were the door-sensor-only tags
from before the hardware repo grew to host multiple board projects.
New tags use the `door_sensor-rev-*` namespace going forward.

Check out a specific revision:
```bash
git checkout door_sensor-rev-a   # or -rev-b
```

## What's in the repo

| Path | What |
|---|---|
| `ble_door_sensor.kicad_pro` / `.kicad_sch` / `.kicad_pcb` | KiCad 7+ project files. |
| `ble_door_sensor.kicad_dru` | Custom design rules. |
| `ble_door_sensor.csv` | BOM exported from KiCad (informational). |
| `fab/` | *(when generated)* Ready-to-fab Gerber zip, drill files, pick-and-place CSV. |
| `bom/` | *(when generated)* BOM with LCSC / Mouser part numbers, suitable for direct order. |
| `TCS40DPR_STM32WB09_v9.kicad_sch` | Legacy standalone schematic from an earlier exploration phase, kept for reference. |

## Quick fab

1. **Tag**: `git checkout door_sensor-rev-a` (or whichever rev you want).
2. **Open** `ble_door_sensor.kicad_pro` in KiCad 7+.
3. **PCB Editor → File → Plot** → Gerber + drill files → submit to
   JLCPCB / PCBWay / OSH Park / Aisler.
4. **BOM**: use `ble_door_sensor.csv` with the LCSC part numbers; most
   fabs will assemble in-house from this BOM directly.

The PCB is hand-solderable at 0603 / 0805 sizes but the WB09KEVx in
QFN32 is best assembled by the fab.

## Design notes

- **MCU**: STM32WB09KEVx (QFN32). On-chip 2.4 GHz BLE.
- **Sensor**: Toshiba TCS40DPR Hall (active-low, single-ended).
  Magnet absent = sensor reads `1` = door open.
- **LED**: active-low, push-pull, on PA1, with a 510 Ω series resistor.
- **BLE matching**: 20 pF series cap → MLPF-NRG-01D3 → 50 Ω microstrip
  → chip antenna (Johanson 2450AT18A0100E or compatible).
- **Battery monitor**: external divider 1 MΩ / 1 MΩ + 1 µF X7R, midpoint
  into PB1 = ADC1_VINP1. Quiescent current ~1.5 µA. (Rev B; hand-soldered
  on Rev A.)
- **VDDA_VCAP**: 0.22 µF decoupling on pin 28. (Rev B; hand-soldered on
  Rev A.)
- **HSE crystal**: 32 MHz. Firmware sets `XTAL_StartupTime` to 800 µs.

For the long-form design history, including the bring-up gotchas (PB3
JTDO default pull-up, ADC sample-and-hold droop with too-small
reservoir cap), see the firmware repo's `HANDOVER.md` at
[`homesensors/firmware`](https://github.com/homesensors/firmware).

## Bring-up / known-good checks

After fabricating a new board:

1. **Visual / DMM**: check VDDA_VCAP cap (pin 28) is populated, no
   solder bridges around the QFN32, divider resistors read ~1 MΩ each.
2. **Power-on**: cell + LED behaviour matches spec — LED blinks at
   ~1 Hz heartbeat in boot phase when magnet absent.
3. **BLE smoke**: with nRF Connect or `bluetoothctl scan on`, the
   device should appear with service-data UUID 0xFCD2. Service data
   byte sequence starts with `44 00 …`.
4. **HA discovery**: HA's BTHome integration auto-discovers the device
   within seconds. Confirm an entity tree with `opening`, `battery`,
   `packet_id`, `timestamp`.

## License

CERN-OHL-P-2.0 — see [LICENSE](../LICENSE) at the hardware-repo
root. Permissive open hardware:
fork the design, fab it, sell physical units, no copyleft strings.
Just preserve the licence + notices.

## Related repos

- [`homesensors/sensorkit`](https://github.com/homesensors/sensorkit) — umbrella, protocol spec, build guide.
- [`homesensors/firmware`](https://github.com/homesensors/firmware) — STM32WB09 firmware.
- [`homesensors/homeassist`](https://github.com/homesensors/homeassist) — HA add-on.
