# Compact-500 REV5 firmware

This package contains the REV5 firmware built with the Z travel limit confirmed
during hardware bring-up on 2026-11-19, its source patch series and DFU image.

## Current behavior

- X direction: `INVERT_X_DIR=false`; positive X moves away from X-MIN.
- X scale: 400 steps/mm. A 200 mm move from X-MIN at 25 mm/s matched the measured travel.
- Y direction: `INVERT_Y_DIR=false` and `INVERT_Y2_VS_Y_DIR` disabled. Both motors move the gantry toward Y-MIN on negative moves. The switch stopped the approach at 25 mm/s.
- Y scale: 320 steps/mm. A 100 mm move from Y-MIN at 25 mm/s matched the measured travel.
- Travel checked on hardware: Y reached 387 mm, then moved to the midpoint at 193.5 mm. X reached its 333 mm limit with Y at that midpoint. The operator confirmed both limits.
- PE3 LOW selects HBP1201D101GND6-4 sensors at I2C address `0x58`; HIGH
  selects the original GZP sensors at `0x6D`.
- The HBP path processes all 24 pressure bits and automatically averages an
  ambient reference for each channel at startup, with pump/valve outputs off.
  Keep both nozzles open during startup. `M3427 T1` and `M3427 T2` report
  pressure, full raw counts, and `vacuum_level` (255 at ambient).
- Ring lights use the original repository timing and startup RGB test,
  followed by lights off. No custom timing sender or white boot preset remains.
- After adjusting the Z endstop, the operator confirmed a 63 mm travel limit and a shared safe height of 30 mm. Marlin and OpenPnP limit Z to 0–63 mm. The post-homing position and OpenPnP Safe Z are both 30 mm. X, Y and Z homing feed rates are 75 mm/s.
- Full `G28` succeeded with both Y motors connected. With the previous build, the final position was X=0, Y=0, Z=31.5 mm, with X-MIN and Y-MIN triggered and Z-MIN released after the configured post-homing move.

The X full-turn movement and return passed on hardware, and the direction was
confirmed by the operator. Both ring lights passed the RGB test after replacing
a defective level shifter. Vacuum pressure scaling was calculated from the
manufacturer transfer formula; it has not been independently checked with a
reference manometer. No complete OpenPnP placement cycle was tested.

See the nozzle rotation checks in [COMPACT-500.md](../../COMPACT-500.md).

## Enclosure fan

J15 on REV5 is controlled by PE2. It starts at 100% duty for three seconds,
then runs at 50% duty with 250 Hz software PWM. Marlin polls X, Y, Y2, Z, A,
and B every 500 ms. An active TMC2209 overtemperature prewarning or overtemperature
flag from any driver switches the fan to 100%. The fan returns to 50% after
a complete poll reports all drivers clear. A missing or invalid response
cannot clear an existing cooling request. Marlin keeps the configured driver
currents. All six UART links were verified after flashing and the operator
confirmed the 50% setting. A real overtemperature transition has yet to be
verified on the machine.

## Restore the source

The patch applies to upstream Marlin commit `7776c7322837fc6065605413f7932ee9d56458a9`:

```sh
git clone https://github.com/sphawes/Marlin.git Marlin
cd Marlin
git checkout -b compact-500-runtime-vacuum 7776c7322837fc6065605413f7932ee9d56458a9
git am /path/to/marlin-compact-500.patch
pio run -e Opulo_Lumen_REV5
```

The final local source commit is `69ecfcf7c652d0422602e4276d994e3088f87779`. The
Apple Silicon configuration included in the patch additionally expects the
local native dfu-util tool described in `COMPACT-500-VACUUM-SENSORS.md`.

## Binary and verification

`firmware.bin` includes a 16-byte DFU suffix; program its application payload
at `0x08000000` on the LumenPnP REV5 STM32F407VE. Flash readback matched the build byte for byte. The Z limit is configured at 63 mm. See `verification.json` and
`source-manifest.json` for hashes and provenance.

Payload SHA-256: `d5e1b3c655e563ca7563c3ecb9bca87e27970d84dab8f50d1fdaf333d33461fd`.
