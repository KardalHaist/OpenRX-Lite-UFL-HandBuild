# JLCPCB order: PCB, stackup, stencil

Order prepared 2026-11-22, not placed yet. Upload the Gerber zip in `production/rev2.3-nc/`.
Screenshots: [PCB form top](images/screenshots/jlc-pcb-form-1-preview.png) ·
[PCB form bottom](images/screenshots/jlc-pcb-form-2.png) ·
[PCB summary](images/screenshots/jlc-pcb-summary.png) ·
[stencil form](images/screenshots/jlc-stencil-form.png) ·
[stencil summary](images/screenshots/jlc-stencil-summary.png)

## PCB

| Setting | Value | Why |
|---|---|---|
| Layers / thickness | 6 / 1.0 mm | Matches the design stackup |
| Detected size | 10 × 11.5 mm | Sanity check that JLC is reading the outline |
| Qty / format | 5 / Single PCB | |
| Material | FR4 TG155 | Auto-selected with 0.2 mm vias; also better for two reflows plus rework |
| Surface finish | **ENIG** (1 µ") | Flat pads for 0.5 mm-pitch stencil printing; survives double-sided reflow and days of handling. OSP was rejected: its film degrades after the first reflow and once the bag is opened. |
| Copper outer / inner | 1 oz / 0.5 oz | KiCad models 35 µm / 15.2 µm |
| Specify stackup | **JLC06101H-3313** | See below |
| Impedance control | No requirement | |
| Via covering | Epoxy filled & capped | Free on 6+ layers; flat surface so paste can't wick into vias |
| Min via | 0.2 mm / (0.3/0.35 mm) | All 83 vias are 0.45 mm on a 0.20 mm drill |
| 4-wire Kelvin test | Yes | Auto-selected with 0.2 mm vias |
| Castellated holes | **No** | rev2.3-nc has none |
| Mark on PCB | Remove mark | No room on a 10 mm board |
| Confirm production file | Yes | Review JLC's CAM output before fabrication |
| Color / silkscreen | Green / white | Best contrast under the microscope |

## Stackup: why JLC06101H-3313

Stackup codes read as **JLC + 06 (layers) + 10 (thickness 1.0 mm) + 1H + prepreg
type**. The first list JLC showed was all `JLC06161H-…`, i.e. **1.6 mm**,
because the thickness field hadn't been set yet
([screenshot](images/screenshots/jlc-stackup-list-at-1.6mm.png)).

[JLC06101H-3313](images/screenshots/jlc-stackup-JLC06101H-3313.png):

| Layer | Material | Thickness | KiCad model |
|---|---|---|---|
| Top (F.Cu) | Copper | 0.035 mm | 0.035 |
| Prepreg | 3313 ×1 | **0.0994 mm** | **0.100** ← under the RF trace |
| L2 (In1, GND) | Copper | 0.0152 mm | 0.0152 |
| Core | | 0.25 mm | 0.16 |
| L3 (In2, +3V3) | Copper | 0.0152 mm | 0.0152 |
| Prepreg | 7628 ×1 | 0.2104 mm | 0.329 |
| L4 (In3, GND) | Copper | 0.0152 mm | 0.0152 |
| Core | | 0.25 mm | 0.16 |
| L5 (In4, GND) | Copper | 0.0152 mm | 0.0152 |
| Prepreg | 3313 ×1 | **0.0994 mm** | **0.100** ← under the Wi-Fi traces |
| Bottom (B.Cu) | Copper | 0.035 mm | 0.035 |

The outer dielectrics, the only ones the microstrip RF traces "see", match the
KiCad model. The inner layers differ but are buried between planes. Total is
about 1.04 mm.

**How much it matters:** the ELRS RF traces are 0.75 mm (SX1281 → FL1) and
2.60 mm (FL1 → U.FL). At 2.44 GHz the wavelength in FR-4 is about 68 mm, so the
longest trace is about λ/26: **electrically short**. Even a 60 Ω trace
(Γ ≈ 0.09) would cost under 0.04 dB.

## Stencil

| Setting | Recommended | Notes |
|---|---|---|
| Framework | No (frameless) | |
| Side | Top + Bottom on a single stencil | Both 10 × 11.5 mm patterns side by side |
| Size | Custom 100 × 100 mm | A small foil is easier to keep flat on the bench |
| Thickness | **0.08 mm** | Area ratio, see below |
| Polishing | Electropolishing | Smooth aperture walls for 0.5 mm-pitch QFNs |
| Apertures | 1:1 per paste layer, or JLC standard | The design already shrinks paste (−0.02 mm, −8 %) |
| Confirm production file | **Yes** | Check thickness and 0201 apertures before cutting |

> **Before checkout:** the stencil screenshots show thickness *Select by
> JLCPCB* (with a remark asking for 0.08 mm), *JLC standard* apertures, the
> standard 380 × 280 size selected, and *Confirm production file: No*. Set
> Confirm production file to **Yes** and select **Custom Size**. Record the final
> values here after ordering: ______

### Area ratio

Area ratio = aperture area ÷ aperture wall area (perimeter × thickness). Below
**about 0.66** (IPC-7525) paste sticks in the stencil instead of releasing.
Apertures below include the project's paste shrink.

| Part | Aperture (mm) | AR @ 0.08 mm | AR @ 0.10 mm |
|---|---|---|---|
| FL1 filter pads | 0.21 × 0.21 | 0.66 (borderline) | 0.53 ✗ |
| U2 LDO (X2SON) | ≈ 0.32 × 0.18 | ≈ 0.72 | ≈ 0.58 ✗ |
| 0201 passives | 0.23 × 0.26 | 0.76 | 0.61 ✗ |
| U1 ESP32 pins | 0.17 × 0.63 | 0.84 | 0.67 (marginal) |
| U3 SX1281 pins | 0.20 × 0.63 | 0.93 | 0.75 |

Open question for the OpenDrone team: the paste shrink leaves 0201 apertures
at about 52 % of pad area. What stencil thickness was it tuned for?

## Cost note

Castellated holes would have added $37. The `rev2.3-nc` variant removes the
need for them ([docs/01](01-nc-variant.md)).
