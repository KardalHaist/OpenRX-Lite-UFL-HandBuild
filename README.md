# OpenRX Lite-UFL: Hand-Build Log (rev2.3-nc)

A personal hand-assembly build of **OpenRX Lite-UFL**, the open-source 2.4 GHz
ExpressLRS receiver by OpenDrone / Incutec
([upstream repo](https://github.com/OpenDrone-hw/OpenRX-Lite-UFL)).

The goal is to order bare boards and a stencil from JLCPCB, assemble both sides
by hand (stencil, solder paste, hot air), flash it, fly it, and then measure it
against a store-bought unit to learn how every part of the design works.

> **Not an official OpenDrone product.** This repo contains a modified variant
> (`rev2.3-nc`). The OSHWA certification (BE000031) and the OpenDrone name apply
> to the upstream design, not to this variant. The upstream README is kept as
> [README.upstream.md](README.upstream.md).

## Status

- [x] Stock design checked (ERC/DRC baseline, Gerbers inspected layer by layer)
- [x] Non-castellated variant `rev2.3-nc` designed and verified ([why](docs/01-nc-variant.md))
- [ ] PCB + stencil order from JLCPCB (settings ready: [docs/02](docs/02-jlc-order.md))
- [ ] Parts order from LCSC (cart ready: [docs/03](docs/03-parts.md))
- [ ] USB-serial adapter, wire, paste check ([tools](docs/03-parts.md#tools-and-consumables))
- [ ] Assembly, bottom side then top side ([plan](docs/04-assembly.md))
- [ ] First power-up and first flash over UART ([flashing](docs/05-flashing.md))
- [ ] Bind to RadioMaster TX12 (ELRS 3.5 or later)
- [ ] Bench measurements against the store-bought reference unit

## What changed from stock

One thing: the four RX / TX / 5V / GND pads.

| | Stock rev2.3 | This build, rev2.3-nc |
|---|---|---|
| Edge pads TP1–TP4 | Castellated half-holes (1.0 mm hole on the board edge) | Plated pad 1.9 × 2.0 mm, 0.4 mm hole, ends 0.25 mm inside the edge |
| JLC castellation option | Required (+$37 on this order) | Not needed |
| Mounting | Wire or flat onto a carrier board | Wire only |
| Circuit, routing, all other parts | — | Unchanged |

The full story is in [docs/01-nc-variant.md](docs/01-nc-variant.md). It covers
why JLC's uploader rejected the stock outline, why the first attempt left the
5 V, TX and RX pads disconnected, and the DRC comparison.

## Repo layout

| Path | What it is |
|---|---|
| `hardware/` | Stock rev2.3 KiCad project, **unmodified** from upstream |
| `hardware/bom/ibom.html` | Interactive BOM (generated from the stock board; part placement is identical in nc). Open it in a browser while placing parts. |
| `hardware-nc/` | The rev2.3-nc variant: project files and the new footprint `SolderPad_Edge_1.9x2.0mm-NC` |
| `production/rev2.3-nc/` | The verified Gerber/drill zip to upload to JLCPCB, plus BOM and placement CSVs |
| `firmware/` | Upstream ExpressLRS layout and target files (unmodified) |
| `docs/` | Build documentation, DRC reports, parts cart, screenshots |

## Docs

1. [The non-castellated variant](docs/01-nc-variant.md)
2. [JLCPCB order: PCB, stackup, stencil](docs/02-jlc-order.md)
3. [Parts, substitutions, tools](docs/03-parts.md)
4. [Assembly plan](docs/04-assembly.md) *(in progress)*
5. [Flashing ExpressLRS](docs/05-flashing.md)
6. [Images index](docs/images/README.md)

## Cloning

The OpenDrone parts library is a git submodule, used by both KiCad projects:

```sh
git clone --recurse-submodules <this repo URL>
# or, in an existing clone:
git submodule update --init
```

## Credits and license

Design by OpenDrone / Incutec BV (maintainer `bastian2001`), released under
[CERN-OHL-S-2.0](LICENSE). This repository is a **Modified** version of that
design. The modification notice required by the licence is in
[NOTICE.md](NOTICE.md), and the modified source is licensed under the same
CERN-OHL-S-2.0 terms.
