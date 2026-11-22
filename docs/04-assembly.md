# Assembly plan (in progress)

Hand assembly of both sides with a stencil, solder paste and hot air. This page
gets filled in as the build happens. Photos go in
[images/photos](images/README.md).

Use the interactive BOM, [`hardware/bom/ibom.html`](../hardware/bom/ibom.html),
open in a browser to highlight each part's pads while placing.

## Parts by side

**Top (F.Cu), 20 parts:** U3 SX1281 (QFN-24), FL1 filter, J1 U.FL, X1 40 MHz
crystal, D1 LED, L1 24 nH, L3 2.0 nH, R2, C1, C3 (15 pF), C4, C23, C25 (1 µF),
C5, C6, C9 (100 nF), C24 (10 nF), C8, C11, C16 (10 µF, 0402).

**Bottom (B.Cu), 22 parts:** U1 ESP32-C3 (QFN-32), U2 LDO (X2SON-4), OSC1 52 MHz
TCXO, AE1 antenna, L4 15 µH (0805), L2 2.5 nH, R3–R5, C10, C15, C28 (1 µF),
C12, C13, C14, C17 (100 nF), C19 (1 nF), C20 (470 nF), C21, C22 (1.2 pF),
C26, C27 (10 nF).

## Plan (to be decided and documented)

- [ ] Which side first, and how to protect it during the second reflow
- [ ] Holding and registering a 10 × 11.5 mm board under the stencil (jig from scrap 1.0 mm FR-4; a deeper pocket for side 2)
- [ ] Paste: type, date, storage
- [ ] Reflow profile (preheat, soak, peak, cool) and preheater use
- [ ] Placement order (smallest and flattest first)

## Inspection checklist (after each side)

- [ ] QFN pins: no bridges, every pin wetted (microscope from all four sides)
- [ ] LED D1 joints. **GPIO8** (LED data) is a strapping pin that must be high for download mode; a short here blocks flashing.
- [ ] π-match C21, L2, C22 and AE1 feed joint (Wi-Fi path)
- [ ] U.FL shell vs. the TX via next to it (a stock mask-opening warning)
- [ ] AE1 orientation: Pulse dot on pad 1

## First power-up (each board)

1. Continuity: **5V–GND** and **3V3–GND** must not be shorts.
2. Bench supply on the **5V pad**, current-limited (start around 50–100 mA).
3. Check the 3.3 V rail.
4. Only then connect the USB-serial adapter (TX, RX, GND only while on the bench supply).
5. Serial terminal at 115200 baud with BOOT held low at power-up. The ROM download-mode banner confirms the ESP32, its crystal and the UART joints.

Then flash: [05-flashing.md](05-flashing.md).

## Build log

| Date | Board # | What happened |
|---|---|---|
| | | |
