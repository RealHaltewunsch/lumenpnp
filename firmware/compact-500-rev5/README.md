# Compact-500 REV5 firmware

This package records the firmware flashed and checked during hardware bring-up
on 2026-11-16. It contains the source patch, the DFU-suffixed application image,
and the byte-for-byte flash-readback result.

## Current behavior

- X direction: `INVERT_X_DIR=false`; positive X moves away from X-MIN.
- Nominal XY travel: 333 x 387 mm. Full travel clearance remains unverified.
- PE3 LOW selects HBP1201D101GND6-4 sensors at I2C address `0x58`; HIGH
  selects the original GZP sensors at `0x6D`.
- The HBP path processes all 24 pressure bits and automatically averages an
  ambient reference for each channel at startup, with pump/valve outputs off.
  Keep both nozzles open during startup. `M3427 T1` and `M3427 T2` report
  pressure, full raw counts, and `vacuum_level` (255 at ambient).
- Ring lights use the original repository timing and startup RGB test,
  followed by lights off. No custom timing sender or white boot preset remains.
- Z homing retains the original settings. With `Z_SAFE_HOMING` enabled,
  `G28 Z` first requires X/Y homing. The slow isolated Z reference test passed;
  the full `G28 Z` test failed with X/Y disconnected.

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

The local source commit is `81450510d5785d86a358e61707185133a746a0ab`. Its documentation was updated after
the binary build; its firmware code matches the flashed application. The
Apple Silicon configuration included in the patch additionally expects the
local native dfu-util tool described in `COMPACT-500-VACUUM-SENSORS.md`.

## Binary and verification

`firmware.bin` includes a 16-byte DFU suffix; program its application payload
at `0x08000000` on the LumenPnP REV5 STM32F407VE. A complete readback matched
all 127488 application bytes. See `verification.json` and
`source-manifest.json` for hashes and provenance.

Payload SHA-256: `2eab1588ebac3e2b0400b3b0e3ddf8176b5e9f7fc1402b8b3a8b8ff51cc11942`.
