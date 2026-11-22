# The non-castellated variant (rev2.3-nc)

## Why it exists

The stock RX, TX, 5V and GND pads (TP1–TP4) are **castellated holes**: plated
1.0 mm holes centered on the board edge that the router cuts in half. JLCPCB
needs a separate castellation process for that, and on this order it added
**$37**. This build only ever solders wires to the pads, so the half-holes buy
nothing. The variant moves the pads fully onto the board.

## Problem 1: JLC's uploader ignored the stock outline

Uploading the stock Gerbers gave a detected size of **15.15 × 12.8 mm**
instead of 10 × 11.5 mm. The preview showed the castellated pads as whole ovals
with green board around them: the outline was being ignored and a board was
drawn around the copper instead.

The stock footprint `SolderPad_1.2x2.0mm` is actually a 1.9 × 4.5 mm plated pad
with a 1.0 mm drill **centered on the edge**, plus four 0.4 mm holes. Two of
those holes (y = 112.2 mm) sit **completely outside the board**, 1 mm past
the edge.

| Test | Change to the Gerbers | Result |
|---|---|---|
| 1 | Removed the two drill-map drawings; deleted a zero-length line at the start of the Edge.Cuts outline | Still wrong |
| 2 | Test 1 plus removing the **8 off-board 0.4 mm drill hits** | **10 × 11.5 mm detected, half-holes shown correctly** |

**Cause:** drill holes entirely outside the outline make JLC's auto-detection
reject the outline. Those holes are in scrap material and would be routed away
anyway. This is worth reporting upstream as a footprint issue.

## Problem 2: the first attempt disconnected 5 V, TX and RX

Attempt 1 replaced the castellated pad with **SMD** pads on F.Cu and B.Cu plus
two 0.4 mm vias off to the sides
([screenshot](images/screenshots/kicad-footprint-attempt1-smd-broken.png)).
DRC reported **3 unconnected pads**. The old through-hole pad existed on **all
six copper layers**, and three inner-layer traces end inside it:

| Pad | Inner trace | Where it ends |
|---|---|---|
| TP3 (5 V) | 0.3 mm on **In2**, to the LDO input | (106.27, 109.52), pad center line |
| TP2 (TX) | 0.09 mm on **In3**, to the ESP32 | Runs down the pad center to y = 110.76 |
| TP1 (RX) | 0.09 mm on **In3**, to the ESP32 | y = 108.8, top edge of the pad |
| TP4 (GND) | Ground pour | Pour wraps any pad |

SMD pads only exist on outer layers, and the side vias missed the traces. A
second bug: the bottom SMD pad had **B.Cu copper with F.Mask**, so it would have
been covered by solder mask. DRC flagged it as "Padstack is questionable."

## The fix: footprint `SolderPad_Edge_1.9x2.0mm-NC`

One plated through-hole pad, so copper exists on every layer again where the
inner traces land
([pad settings](images/screenshots/kicad-pad-properties-v2-tht.png)):

| Field | Value |
|---|---|
| Type | Through-hole, pad number `1` |
| Shape | Rounded rectangle, 25 % |
| Position | (0, −1.25) from the footprint origin, which sits on the board edge |
| Size | 1.9 × 2.0 mm, so it runs from y = −2.25 to −0.25 |
| Hole | 0.4 mm round, plated |
| Copper | All layers, **remove unused layers off** |
| Mask / paste | F.Mask + B.Mask / none (stock pads get no paste) |

The pad stops **0.25 mm** from the edge, clearing the 0.20 mm copper-to-edge
rule. A single center hole is used because two extra holes next to it would
break the 0.20 mm hole-to-hole rule.

TP1–TP4 were swapped with **Change Footprint → selected footprints only**
([screenshot](images/screenshots/kicad-change-footprints-tp1-tp4.png)).
**TP5 (BOOT) was deliberately left alone**: it uses the same library name but
carries a custom 1.2 × 1.0 mm SMD pad edited on the board. "Update footprints
from library" on the whole board would turn it into a castellated pad.

## DRC comparison

| Finding | Stock rev2.3 | nc attempt 1 (SMD) | **nc v2 (THT)** |
|---|---|---|---|
| unconnected_items | 0 | **3** | **0** |
| track_dangling | 0 | 4 | 0 |
| padstack | 0 | 4 | 0 |
| copper_edge_clearance | 4 (castellations, expected) | 0 | 0 |
| connection_width | 5 | 5 | 5 |
| courtyards_overlap (TP4/TP5) | 1 | 1 | 1 |
| lib_footprint_mismatch | 9 | 9 | 9 |
| solder_mask_bridge | 32 | 37 | 29 |
| **Total violations** | **51** | **60 + 3 unconnected** | **44** |

Reports: [stock](drc/stock-rev2.3_DRC.rpt) ·
[attempt 1](drc/nc-attempt1-smd-pads_DRC.rpt) ·
[v2](drc/nc-v2-tht-pads_DRC.rpt)

Every warning left in v2 also exists in stock:

- **connection_width:** GND pour necks of 0.066–0.078 mm, slivers in the pour, not signal traces.
- **courtyards_overlap TP4/TP5:** TP5 kept the tall castellated courtyard after its pad was shrunk. Copper gap is 0.20 mm (rule 0.09 mm).
- **lib_footprint_mismatch:** board copies were tuned on purpose. Never update all footprints from library.
- **solder_mask_bridge:** the "RX" mask-opening text on the back, and J1's mask opening touching a TX via. The count changes with zone refill. Inspect the U.FL-to-TX-via area after reflow.

## Gerber verification (the zip to upload)

`production/rev2.3-nc/OpenRX-Lite-UFL-rev2.3_V2.THT-NC-Pads.zip` was checked directly:

- Pad flashes for TP1–TP4 on **all six copper layers**.
- In2 5 V trace ends inside TP3; In3 TX passes through TP2; In3 RX lands on TP1's top edge.
- Drill file: the 0.20 mm vias plus exactly four 0.4 mm pad holes; no 1.0 mm holes, nothing off-board.
- All copper within the outline; JLC detected 10 × 11.5 mm.

## Known loose ends

- [x] Board file restored. The working copy was accidentally flipped (Select All + F: mirrored, top and bottom swapped) and saved after the export. It was flipped back and checked: every footprint, all 274 track segments and 83 vias match the stock positions and layers (within 0.000002 mm), and TP1–TP4 match the exported Gerbers.
- [ ] Schematic: set the footprint field of TP1–TP4 to `OpenRX-Shared:SolderPad_Edge_1.9x2.0mm-NC`, or a later "Update PCB from Schematic" brings the castellated pads back.
- [ ] Page Settings revision → `rev2.3-nc` (the exported Gerbers still say `rev2.3` in their header).
- [ ] The new footprint's description still reads "Solder pad 1.2x2.0mm…" (copied from the original).
- **Soldering note:** BOOT (TP5) is only 0.2 mm from GND (TP4). Keep solder off the top edge of the GND pad, or the receiver boots into download mode every time. The same gap makes a tweezers bridge easy when you *want* download mode.
