# Parts, substitutions, tools

The schematic is the authoritative BOM (upstream rule: no hand-written second
BOM). The table below was extracted from both schematic sheets and is here to
check the cart against. 42 parts per board: 20 top, 22 bottom.

## Per-board BOM (from the schematic)

| Qty | Value | Refs | LCSC (schematic) | Package |
|---|---|---|---|---|
| 1 | ESP32-C3FH4 (4 MB flash in package) | U1 | C2858491 | QFN-32 5×5 |
| 1 | SX1281IMLTRT | U3 | C2151551 | QFN-24 4×4 |
| 1 | TLV75533PDQNR (3.3 V LDO) | U2 | C2861882 | X2SON-4 1×1 |
| 1 | 2450FM07D0034T (2.45 GHz filter) | FL1 | C2651081 | 1.0×0.5 |
| 1 | OW7EL89CENUNFAYLC-52M (52 MHz TCXO) | OSC1 | C22434896 | 2.0×1.6 |
| 1 | CJ17-400001010B20 (40 MHz crystal) | X1 | C2875272 | 1.6×1.2 |
| 1 | U.FL-R-SMT-1(80) | J1 | C88374 | |
| 1 | 2450AT18A100E (Wi-Fi antenna) | AE1 | C89334 | 3.2×1.6 |
| 1 | XL-1010RGBC-WS2812B (status LED) | D1 | C5349953 | 1×1 |
| 1 | 15 µH | L4 | C383402 | 0805 |
| 1 | 24 nH | L1 | C206441 | 0201 |
| 1 | 2.5 nH | L2 | C206435 | 0201 |
| 1 | 2.0 nH | L3 | C86125 | 0201 |
| 4 | 10 kΩ | R2–R5 | C106225 | 0201 |
| 2 | 15 pF | C1, C3 | C85891 | 0201 |
| 2 | 1.2 pF | C21, C22 | C85895 | 0201 |
| 1 | 1 nF | C19 | C66942 | 0201 |
| 3 | 10 nF | C24, C26, C27 | C2168164 | 0201 |
| 7 | 100 nF | C5, C6, C9, C12, C13, C14, C17 | C76939 | 0201 |
| 1 | 470 nF | C20 | C85926 | 0201 |
| 6 | 1 µF | C4, C10, C15, C23, C25, C28 | C76935 | 0201 |
| 2 | 10 µF | C8, C16 | C15525 | 0402 |
| 1 | 10 µF | C11 | C76991 | 0402 |

## Substitutions in the LCSC cart

Cart export: [lcsc-cart-2026-11-22.csv](parts/lcsc-cart-2026-11-22.csv)

| Ref | Stock part | In cart | Verdict |
|---|---|---|---|
| FL1 | C2651081 `2450FM07D0034T` (out of stock) | C6054595 `2450FM07D0034001T` | **Same part, new number.** Johanson lists 2450FM07D0034T as its legacy P/N. |
| C24, C26, C27 | Murata GRM033R61A103 | Kyocera AVX `0201ZD103KAT2A` (C5348624) | Equivalent: 10 nF, 10 V, X5R, 0201 |
| 7 × 100 nF | Murata GRM033R61E104 | Walsin `0201X104K250CT` (C880779) | Equivalent: 100 nF, 25 V, X5R, 0201 |
| AE1 | Johanson `2450AT18A100E` (unavailable; the B variant too) | Pulse `ANT3216LL00R2400A` (C22423581) | **Different antenna, same footprint.** See below. |

### AE1: Pulse instead of Johanson

| | Johanson 2450AT18A100 | Pulse ANT3216LL00R2400A | AE1 footprint |
|---|---|---|---|
| Body | 3.2 × 1.6 × 1.3 mm | 3.2 × 1.6 × 1.2 mm | Fits either |
| End terminals | 0.5 ± 0.3 mm | 0.4 ± 0.2 mm | Pads 1.0 × 1.75 mm, inner edges ±1.08 mm |
| Pin 1 | Feed | S1 = feed (dot on top) | WIFI net |
| Pin 2 | NC | S2 = isolated solder anchor | Unconnected |

- **Orientation: the dot (S1) goes on pad 1**, the end toward L2 / C21.
- Tuning differs: the π-match (C21 1.2 pF, L2 2.5 nH, C22 1.2 pF) was tuned for the Johanson part, and both antennas are far from their evaluation boards on a 10 mm board.
- AE1 only carries the ESP32's Wi-Fi (web UI and OTA updates). The ELRS link is on the U.FL. The bench link budget at 3 m leaves roughly 25–40 dB of margin even with a badly detuned antenna, so the expected effect is shorter Wi-Fi range, not a dead link.
- Test idea: Wi-Fi RSSI / range of this build compared with the store-bought unit at the same distances.

## Quantities in the cart (2 complete IC sets)

| Item | Per board | In cart |
|---|---|---|
| ESP32-C3, SX1281, TCXO, FL1 | 1 each | 2 each, so only **2 complete boards** |
| LDO, U.FL, Pulse antenna | 1 | 5 |
| Crystal, 15 µH, LED | 1 | 10 |
| 1 µF 0201 | 6 | 50 |
| Other 0201s | 1–7 | 100 each |

Worth adding before checkout: a third IC set, and 100 × 1 µF.

## Tools and consumables

- [ ] **3.3 V USB-serial adapter** (CP2102 or CH340 with a 3.3 V jumper). ESP32-C3 pins are not 5 V tolerant.
- [ ] **30 AWG silicone wire** for the flashing pigtail and FC wiring.
- [ ] Optional: **4-pin 2.54 mm pogo-pin header.** TP1–TP4 are on 2.54 mm pitch, so it makes a solderless flashing fixture.
- [ ] **Solder paste check:** Type 4 (20–38 µm) or Type 5 (15–25 µm). Type 3 (25–45 µm) is too coarse for the 0.17 mm ESP32 apertures (rule of thumb: about 5 particles across the narrowest aperture). Check the date and storage too.
