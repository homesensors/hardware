# homesensors / hardware

[![License: CERN-OHL-P-2.0](https://img.shields.io/badge/License-CERN--OHL--P--2.0-blue.svg)](LICENSE)

Open-hardware KiCad projects for the
[`homesensors`](https://github.com/homesensors/sensorkit) DIY
smart-home sensor kit. Multiple PCBs live here as peer sub-projects
under their own subfolders; each board is a self-contained KiCad
project with its own README, BOM, and revision history.

## Board family

| Subfolder | Status | What it is |
|---|---|---|
| [`door_sensor/`](door_sensor/) | **Rev A fabricated, Rev B at fab** | Single-purpose BLE door sensor (STM32WB09 + TCS40DPR Hall + CR2032). The first board in the family; what the rest of `homesensors` was originally designed around. |
| `motherboard_ble/` | *Planned* | Modular-architecture motherboard with on-board STM32WB09 BLE radio + power input + mikroBUS extension socket. Per [`docs/modular-spec.md`](https://github.com/homesensors/sensorkit/blob/main/docs/modular-spec.md). |
| `motherboard_wifi/` | *Future* | ESP32-based motherboard, same connector contract. |
| `motherboard_zigbee/` | *Future* | EFR32MG24-based motherboard. |
| `motherboard_lora/` | *Future* | STM32WLE5-based motherboard. |
| `extension_door/` | *Planned* | Door-contact sensor as a modular extension (replaces the standalone `door_sensor/` once the modular line stabilises). |
| `extension_temp_humidity/` | *Future* | Temperature + humidity I2C extension. |
| *(more extensions)* | *Future* | Motion, leak, fire, power, audio, vibration… see modular-spec §5.4 ID table. |

## Repository layout

```
hardware/
├── README.md                ← this file
├── LICENSE                  ← CERN-OHL-P-2.0, applies to every board
├── .gitignore               ← KiCad cruft (backups, fp-info-cache, *.kicad_prl)
├── libraries/               ← shared symbol/footprint libs (added when there's something to share)
│   ├── homesensors.kicad_sym   (planned)
│   └── homesensors.pretty/     (planned)
└── <board>/                 ← per-board KiCad project (one subfolder per PCB)
    ├── README.md            ← board-specific docs + revision history
    ├── <name>.kicad_pro
    ├── <name>.kicad_sch
    ├── <name>.kicad_pcb
    ├── <name>.kicad_dru     ← design rules
    ├── <name>.csv           ← BOM export
    ├── fab/                 ← (when generated) Gerbers + drill + P&P
    ├── bom/                 ← (when generated) sourcing-ready BOM with LCSC PNs
    └── 3d/                  ← (when generated) board-level renders
```

Each board's project lives in its own subfolder so that KiCad's
`<project>-backups/` dir stays adjacent to the project that produced
it, and so that `fab/` outputs from one board don't mix with another.

## Versioning

Tags are namespaced by board: `door_sensor-rev-a`, `motherboard_ble-rev-a`,
etc. Each board carries its own revision sequence and is fabbed
independently.

For backward-compatibility with the early period when `hardware/` had
only the door sensor at its root, the unscoped tags `pcb-rev-a` and
`pcb-rev-b` are preserved as aliases pointing at the same commits as
`door_sensor-rev-a` and `door_sensor-rev-b` respectively. Don't add
unscoped tags going forward.

## License

CERN-OHL-P-2.0 — see [LICENSE](LICENSE). Permissive open hardware:
fork the designs, fab them, sell physical units, no copyleft strings.
Just preserve the licence + notices.

## Related repos

- [`homesensors/sensorkit`](https://github.com/homesensors/sensorkit) — umbrella docs, protocol spec, modular architecture spec, image gallery.
- [`homesensors/firmware`](https://github.com/homesensors/firmware) — MCU firmware (currently for `door_sensor`; modular framework planned).
- [`homesensors/homeassist`](https://github.com/homesensors/homeassist) — Home Assistant add-on (Tier 1.5 ACK daemon).
