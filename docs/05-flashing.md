# Flashing ExpressLRS

The board has no USB port. The ESP32-C3 has a ROM bootloader (in mask ROM, so it
can't be erased) that accepts firmware over UART0, and the RX and TX pads *are*
UART0. The first flash on a blank chip has to go over UART with BOOT held low.
Later updates can go over Wi-Fi or Betaflight passthrough.

## Which target to pick

OpenRX Lite-UFL is **not** in the official ExpressLRS targets list. The
`firmware/targets_entries.json` in this repo is written in that list's format
but hasn't been merged. Its layout file is identical to ExpressLRS's generic
ESP32-C3 receiver layout (`Generic C3 2400.json`) except for one line:

| Setting | OpenRX Lite-UFL | Generic C3 2400 |
|---|---|---|
| Serial RX / TX | 20 / 21 | 20 / 21 |
| Radio MISO / MOSI / SCK / NSS | 5 / 4 / 6 / 7 | same |
| Radio BUSY / DIO1 / RST | 3 / 1 / 2 | same |
| Power values | [13] (fixed, about 20 mW) | same |
| LED | GPIO8, GRB | same |
| Button | not set | GPIO9 (BOOT pad; harmless, never pressed) |
| Firmware | `Unified_ESP32C3_2400_RX` | same |

The Configurator in use didn't show a "Generic" category. These official targets
use `Generic C3 2400.json` **with no overrides** and are therefore identical:

- **Flycolor 2.4GHz Nano RX** ← chosen
- HDZero 2.4GHz AIO RX, Konex FC 2.4GHz RX, NewBeeDrone 2.4Ghz RaceSpec RX, OMPHOBBY OFS3+ 2.4 GHz RX

**Avoid:** BETAFPV AIO V2, Spedix, THOBBY, BotLabDynamics and UPOINT Lite. Same
base layout, but they override the LED, so the WS2812B wouldn't work.
RadioMaster receivers are all ESP8285, ESP32 or LR1121-based and won't run on
this board.

Optional: after the first flash, open 10.0.0.1 → hardware layout and paste
`firmware/OpenRX Lite-UFL 2400.json` to run OpenDrone's exact layout.

## Before flashing

- [ ] TX12 internal module updated to ELRS **3.5 or later** (it was 3.3.1). Stay on the same major version on both sides.
- [ ] Note the exact TX version and the binding phrase.

## Wiring (3.3 V USB-serial adapter)

| Adapter | Receiver pad |
|---|---|
| TXD | **RX** (TP1, GPIO20) |
| RXD | **TX** (TP2, GPIO21) |
| GND | GND (TP4) |
| 5V | 5V (TP3). Use only one power source. |

- Adapter logic jumper on **3.3 V**.
- Never connect the adapter's 3V3 pin. The board makes its own 3.3 V with U2.
- For a board's very first power-up use the current-limited bench supply on 5V and connect only TXD/RXD/GND.

## Download mode

Bridge **BOOT (TP5) to GND (TP4)** with tweezers (the pads are 0.2 mm apart),
apply power, hold about 1 s, then release. The chip reads GPIO9 once at reset.
GPIO8 must also be high; the board's pull-up handles it.

## ExpressLRS Configurator

1. Firmware version: **Official releases**, same version as the TX12.
2. Device category **Flycolor** → **Flycolor 2.4GHz Nano RX**.
3. Flashing method: **UART**.
4. Regulatory domain: **ISM 2400** (US/FCC).
5. Binding phrase: same as the TX.
6. Put the receiver in download mode, choose the COM port, click **Flash**.

A good log shows "Chip is ESP32-C3", the "Writing at 0x…" progress, then "Hash of data verified".

| Error | Likely cause |
|---|---|
| Failed to connect / no serial data | Not in download mode, or TX/RX not crossed |
| Wrong boot mode detected | BOOT wasn't low *before* power-up |
| No COM port | CP210x or CH340 driver missing |
| Drops mid-write | Loose wire or weak 5 V; use the bench supply |

## After flashing

- Power-cycle without BOOT. With the TX off, the LED shows the waiting pattern; with the TX on, it goes solid once bound.
- Check RSSI and link quality in the ELRS Lua script on the TX12.
- Later updates: the **WiFi** or **BetaflightPassthrough** method in the Configurator.
- The Lua menu will show the Flycolor name. That only affects the name.

## Mounting note (from upstream)

Keep the external ELRS antenna (U.FL) away from the on-board Wi-Fi antenna.
