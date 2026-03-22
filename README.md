# Neptune 4 Pro — Klipper Config Backup

Automated backup powered by [Klipper-Backup](https://github.com/Staubgeborener/klipper-backup).

**Printer:** Elegoo Neptune 4 Pro
**Firmware:** [Kalico](https://github.com/KalicoCrew/kalico) (Klipper fork)
**Host:** Rockchip RK3328 (ZNP-K1 board)

---

## Config Structure

The configuration is split into modular include files for easier debugging. Any include can be commented out in `printer.cfg` to isolate a subsystem.

### `printer.cfg`
The main entry point. Contains only:
- All `[include ...]` directives (organized by category)
- Core base config: `[mcu]`, `[printer]`, `[danger_options]`, `[virtual_sdcard]`, `[exclude_object]`, `[skew_correction]`, `[force_move]`, `[idle_timeout]`, `[display_status]`, `[gcode_arcs]`
- Auto-generated `SAVE_CONFIG` block (bed mesh, PID values, probe calibration)

### Include File Reference

#### Core Hardware
| File | Contents |
|---|---|
| `steppers.cfg` | X/Y/Z stepper motors + TMC2209 UART drivers |
| `th-nitehawk.cfg` | LDO Nitehawk-36 toolboard MCU, part cooling fan, heatbreak fan, toolhead LEDs, ADXL345 |
| `extruder.cfg` | Extruder tuning (rotation_distance, pressure_advance), verify_heater, firmware_retraction, M109 override |
| `bed.cfg` | Dual-zone bed heaters (inner + outer), verify_heaters, M140/M190 overrides for dual-zone control, M141/M191 chamber heating |
| `fans.cfg` | Bentobox filtration fan, controller temperature fan, M106 override (routes P3 to Bentobox) |
| `temperature_sensors.cfg` | Rockchip host, STM32 MCU, and chamber temperature sensors |

#### Motion & Probing
| File | Contents |
|---|---|
| `eddy-ng.cfg` | BTT Eddy probe (eddy-ng fork) configuration |
| `homing.cfg` | `[bed_mesh]`, `[adxl345_probe]`, `[homing_override]`, `[z_tilt]`, `[screws_tilt_adjust]`, `[axis_twist_compensation]`, plus homing/leveling macros: `FULL_HOME`, `GET_BED_MESH`, `TEST_ZERO`, `Bed_Level_Screws_Tune`, `Auto_Full_Bed_Level` |
| `awd-sync.cfg` | AWD dual-Y motor synchronization |
| `input_shaper.cfg` | Input shaper values (MZV, X/Y tuned frequencies + damping ratios), commented `[resonance_tester]` block |

#### Macros
| File | Contents |
|---|---|
| `macros.cfg` | `PRINT_START`, `PRINT_END`, `PAUSE`, `RESUME`, `CANCEL_PRINT`, `_CLIENT_EXTRUDE`, parking macros (`PARK_TOOLHEAD`, `PARK_CENTER`), PID tuning macros |
| `led-control.cfg` | Frame and part lighting via GPIO shell commands |
| `squiggly_purge.cfg` | Squiggly purge line macro (replaces straight purge line) |
| `sp_mmu.cfg` + `sp_mmu_code.cfg` | [LH Stinger Pico MMU](https://github.com/lhndo/LH-Stinger/wiki/Pico-MMU) settings and logic — **must load last** |
| `moonraker_obico_macros.cfg` | Obico (The Spaghetti Detective) integration macros — **must load last** |

#### Calibration
| File | Contents |
|---|---|
| `calibration.cfg` | `FORCE_SET_POS`, `Z_ENDSTOP_CALIBRATE` |
| `reshelper.cfg` | Resonance helper utility |
| `motion-test.cfg` | Motion testing macros |

#### Utilities
| File | Contents |
|---|---|
| `klipper-backup.cfg` | `update_git` macro to trigger this backup |
| `klipper-maintenance.cfg` | KlipperMaintenance integration |
| `crowsnest.conf` | Crowsnest webcam service config |
| `moonraker.conf` | Moonraker API server config |

#### Optional / Debug (commented out by default)
| File | Contents |
|---|---|
| `chopper_tune.cfg` | TMC chopper tuning results |
| `klipper-tmc-autotune.cfg` | TMC autotune plugin config |
| `adxlmcu.cfg` | Standalone ADXL345 MCU (used for resonance testing without toolboard) |
| `vibrations-profiler.cfg` | Vibrations profiler config |

---

## Hardware Summary

| Component | Detail |
|---|---|
| Mainboard | MKS ZNP-K1 (STM32F401) |
| Toolboard | LDO Nitehawk-36 (RP2040) |
| Probe | BTT Eddy (eddy-ng) + ADXL345 tap via Nitehawk |
| Extruder | LDO Orbiter v2.5 |
| Y-axis | AWD (dual stepper_y + stepper_y1) |
| Z-axis | Dual Z with z_tilt |
| MMU | [LH Stinger Pico MMU](https://github.com/lhndo/LH-Stinger/wiki/Pico-MMU) (PICO_MMU — RP2040) |
| Enclosure | Bentobox filtration |
| Remote access | Obico (moonraker-obico) + `neptune.sirddail.net` |
