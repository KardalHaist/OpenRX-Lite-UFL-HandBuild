# Modification notice (CERN-OHL-S-2.0, section 3.3)

**Original design:** OpenRX Lite-UFL rev2.3, by OpenDrone / Incutec BV.
Source location: https://github.com/OpenDrone-hw/OpenRX-Lite-UFL

**This repository is a Modified version of that Covered Source.**

## Modifications

**2026-11-22**: by the owner of this repository (GitHub: KardalHaist)

- Added `hardware-nc/`, a variant of the rev2.3 board named `rev2.3-nc`.
  - New footprint `SolderPad_Edge_1.9x2.0mm-NC`: a 1.9 × 2.0 mm plated
    through-hole pad with a 0.4 mm drill, ending 0.25 mm inside the board edge.
  - TP1–TP4 (RX, TX, 5V, GND) use the new footprint instead of the castellated
    `SolderPad_1.2x2.0mm`. TP5 (BOOT) is unchanged.
  - No changes to the schematic circuit, nets, routing, or any other part.
- Added the production files for this variant (`production/rev2.3-nc/`).
- Added build documentation (`docs/`) and the interactive BOM
  (`hardware/bom/ibom.html`).
- Renamed the upstream `README.md` to `README.upstream.md` and added a new
  `README.md` for this build.

The stock design in `hardware/` and the files in `firmware/` are unmodified.

## Licence

The modified Covered Source is licensed as a whole under CERN-OHL-S-2.0
(see [LICENSE](LICENSE)). All original Notices are retained.
