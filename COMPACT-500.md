# LumenPnP Compact 500

This branch tracks a compact LumenPnP variant based on upstream LumenPnP
`v4.1.0`.

Planned mechanical baseline:

- 500 mm 2020 frame extrusions
- 450 mm X and Y linear rails
- 500 mm wide staging plate
- X cable-chain support shortened by 100 mm to match the 500 mm X extrusion,
  while retaining the original end and mounting geometry
- X drag-chain length adjusted to the shortened support and validated over the
  complete X travel (provisional reduction: about five 20.5 mm links)
- Target PCB capability: up to 200 mm x 200 mm

## Firmware and OpenPnP configuration (2026-09-18)

The companion `../Marlin` checkout targets the confirmed REV5 motherboard.
X and Y home to MIN at the front-left origin (0, 0). Nominal travel is
X=333 mm and Y=387 mm: the stock 433/487 mm travel minus the 100 mm reduction
of each frame axis. The travel limits were checked during the 2026-11-18 hardware bring-up.

`openpnp/machine.xml` uses the same soft limits. The bottom camera remains
centered on the staging plate, whose width changes from 600 to 500 mm while
its depth is unchanged. Its nominal X coordinate is therefore shifted by
-50 mm (218.151 to 168.151); Y=151.439 and Z=5.7 remain unchanged. This is a
starting position for camera calibration, not a measured calibration result.
Head-camera/nozzle offsets are unchanged. The repository configuration must
be imported or reconciled with the active OpenPnP configuration separately.

The KiCad vacuum sensors are HBP1201D101GND6-4. The fitted PE3-to-GND jumper
selects the firmware's HBP protocol at 7-bit I2C address 0x58. `M3427 T1/T2`
and the matching OpenPnP read commands handle both vacuum channels.

Upstream source: <https://github.com/opulo-inc/lumenpnp>

## Hardware bring-up update (2026-11-16)

The flashed REV5 firmware is now packaged under
[`firmware/compact-500-rev5`](firmware/compact-500-rev5/README.md), including
the upstream source patch, binary, and successful flash-readback verification.
X direction was corrected and confirmed: positive X moves away from X-MIN.
Both ring lights work with the original repository LED timing after replacing
a defective level shifter; the temporary white boot preset was removed.

The HBP sensors automatically zero at startup with open nozzles and pump/valve
outputs off. OpenPnP reads both channels through `M3427` and `vacuum_level`.
The slow isolated Z reference test passed, but full `G28 Z` still requires X/Y
homing and failed when those axes were disconnected.

## Motion checks (2026-11-18)

Both Y motors move the gantry toward Y-MIN on negative moves. Full `G28`
completed at X=0, Y=0, Z=31.5 mm. At 25 mm/s, the measured 200 mm X move
and 100 mm Y move confirmed 400 steps/mm for X and 320 steps/mm for Y.
The operator confirmed Y=387 mm and X=333 mm, with X tested at Y=193.5 mm.

Z travel was confirmed from 0 to 56 mm. Marlin and the OpenPnP controller axis
now limit the commanded travel to this range. The current firmware moves Z to 24.5 mm after homing. The OpenPnP
controller home coordinate is set to the same value.
