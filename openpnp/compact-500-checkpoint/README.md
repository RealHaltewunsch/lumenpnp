# Compact-500 OpenPnP checkpoint

Saved from the working Windows profile on 2026-11-21 after the N2 nozzle tip calibrations. This captures the machine setup before the first PCB placement test.

- Visual homing uses `ResetToFiducialLocation`. The advanced Top camera correction is enabled. The advanced Bottom camera correction is disabled. Bottom Vision is enabled.
- All six N1 tips and all six N2 tips have saved runout measurements. Both racks have four waypoints and two vision templates per tip. The right rack includes the XY adjustment made after calibrating the N2 offset.
- The parts and packages are included. The profile has no configured feeder or board.

## Restore on the Compact-500

Close OpenPnP and back up the target configuration directory. Copy the six XML files and the `org.openpnp.vision.TemplateImage` directory into the target profile, normally `%USERPROFILE%\.openpnp2` on Windows. Keep the image directory name so OpenPnP can resolve the template references. After reopening, check the controller port, camera assignments and physically mounted tips before homing.

## First placement test

Mount the FTP test board, set its surface Z and verify its board fiducials. Configure one feeder and confirm pickup and part alignment through Bottom Vision. Place the first few parts step by step before running a full board. [Opulo validation guide](https://docs.opulo.io/openpnp/v4-1/controlled-validation-test/introduction/)
