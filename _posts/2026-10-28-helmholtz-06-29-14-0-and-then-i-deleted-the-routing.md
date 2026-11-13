---
layout: post
title: "Part 6. 29 → 14 → 0, and then I deleted the routing"
date: 2026-10-28 14:00:00 +0900
categories: [helmholtz]
tags: [hardware, pcb, firmware, keyboard]
series: "Helmholtz: a keyboard down to the microsecond"
---

*Helmholtz: a keyboard down to the microsecond — part 6 of 7.*

The new production board, A_PROD2, puts a 144-ball BGA (0.8 mm pitch) on a 4-layer PCB. Escaping all those balls through only 4 layers is the hard part of the whole board.

### The autorouter grind

- **First pass**: 29 nets unrouted, 534 vias. The two inner layers were completely saturated.
- **Second pass**: 29 → 14 unrouted after 6.6 hours, every one of them around the BGA.
- **The fix list**: tighter inner-layer rules (0.10 mm trace / 0.10 mm space), moving the vibration sensor from SPI2 to SPI4 to get out of a congested corner, and tying the BGA's ground balls together on the top layer, which cut ground vias from 23 to 4.
- **A discovery**: the autorouter version I was using (Freerouting 2.4.1) doesn't have the option I thought was excluding the power planes. It had been routing plane nets as ordinary traces the whole time.

That night the board hit **0 unrouted, 0 DRC errors, 0 ERC errors, 490 vias** in the final set.

### Safety over convenience

There was one real disagreement along the way. Swapping the planes (ground on the top layer, power on the bottom) would make routing harder. But the top layer faces the metal switch plate. With power on that layer, an insulating film between the PCB and the plate becomes mandatory, and one scratch could short the board. I chose ground on top. Routing convenience isn't worth a failure mode that shows up years later in a customer's hands.

### Things that went wrong

- A fabrication script wiped a hand-written notes file during a cleanup step, and it couldn't be recovered. Since then, human-written documents live in their own folder that no script touches.
- A circuit review flagged a major issue: the paths from the MCU's VCAP pins to their capacitors measured 308–407 mΩ, against a 100 mΩ datasheet limit. Post-processing brought them to 17–29 mΩ without rerouting the board.

### Conditional GO, and then a different choice

On October 29 the final set (490 vias, 21 BOM lines, 154 parts) went through the pre-order audit: **conditional GO, zero blockers**. Before paying, seven questions had to go to MADPCB, covering things like black core with matte white, the real stackup and dielectric constant for 90 Ω USB, and whether they could hold 0.10 mm inner-layer rules.

Then I decided to route A_PROD2 by hand in KiCad. The autorouted board passed every check, but the same run didn't always reproduce the same result (one re-run left 2 nets unrouted), and on a board meant to be a premium product I want to understand every trace on it. From here on, the scripts only check my work: DRC, schematic parity, USB pair lengths and widths, plane continuity, VCAP path resistance.
