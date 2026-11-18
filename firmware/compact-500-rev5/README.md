# Compact-500 REV5 firmware

This package records the firmware flashed and checked during hardware bring-up
on 2026-11-18. It contains the source patch series, the DFU-suffixed application image,
and the byte-for-byte flash-readback result.

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
- Full `G28` succeeded with both Y motors connected. The final position was X=0, Y=0, Z=31.5 mm, with X-MIN and Y-MIN triggered and Z-MIN released after the configured post-homing move.

The X full-turn movement and return passed on hardware, and the direction was
confirmed by the operator. Both ring lights passed the RGB test after replacing
a defective level shifter. Vacuum pressure scaling was calculated from the
manufacturer transfer formula; it has not been independently checked with a
reference manometer. No complete OpenPnP placement cycle was tested.

## Restore the source

The patch applies to upstream Marlin commit `7776c7322837fc6065605413f7932ee9d56458a9`:

```sh
git clone https://github.com/sphawes/Marlin.git Marlin
cd Marlin
git checkout -b compact-500-runtime-vacuum 7776c7322837fc6065605413f7932ee9d56458a9
git am /path/to/marlin-compact-500.patch
pio run -e Opulo_Lumen_REV5
```

The final local source commit is `864135f9e3aa86e0e08610fb9e0c2ffa59e99b62`. The
Apple Silicon configuration included in the patch additionally expects the
local native dfu-util tool described in `COMPACT-500-VACUUM-SENSORS.md`.

## Binary and verification

`firmware.bin` includes a 16-byte DFU suffix; program its application payload
at `0x08000000` on the LumenPnP REV5 STM32F407VE. A complete readback matched
all 127488 application bytes. See `verification.json` and
`source-manifest.json` for hashes and provenance.

Payload SHA-256: `f70d98fc72b19552dcb64d042f602b9187d6e94a95600b4b98e9df1cb47435d5`.
