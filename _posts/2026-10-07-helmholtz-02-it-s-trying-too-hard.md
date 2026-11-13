---
layout: post
title: "Part 2. \"It's trying too hard\""
date: 2026-10-07 10:00:00 +0900
categories: [helmholtz]
tags: [hardware, pcb, firmware, keyboard]
series: "Helmholtz: a keyboard down to the microsecond"
---

*Helmholtz: a keyboard down to the microsecond — part 2 of 7.*

The brief I wrote for the look of the board was one sentence: Helmholtz should feel heavy, dignified and luxurious, in the spirit of Patek Philippe.

### Rejecting the watch movement

The first concept render took that literally. It treated the PCB like a watch movement: Geneva stripes, gold traces, decorative patterns everywhere. My reaction was immediate: too busy, not clean. It was trying too hard.

A luxury watch movement is beautiful because it's precise, not because it's decorated. So I went the other way: **matte white solder mask on a black-core FR-4 board, with no decorative patterns at all.** That one decision shaped everything after it.

- No exposed pins or headers. The programming port became a Tag-Connect footprint, just pads with no connector.
- Small packages wherever performance is the same.
- Later the logo came off the PCB too, since the outer layers are copper planes and nobody sees the board once it's assembled. The logo lives on the case and keycaps.
- The USB-C daughterboard has no exposed copper on its front face, because it will sit against a stainless-steel housing and I don't want any chance of a short.

### A dev board with the keyboard and the tester on one PCB

To measure latency, I need two boards: A, the keyboard controller, and B, a tester that fakes a key press and times the USB report. My first thought was to order them as one panel and snap them apart like a Gundam model kit. In the end it got simpler: put A and B side by side on a single 2-layer board (87.5 × 68.6 mm) and wire A's USB data lines straight into B.

Since both share USB-C ports through a mux, the silkscreen literally says "NOT both USB-C at once!" It's a dev board, not a product. It's allowed to have warnings printed on it.

### PCBWay vs. JLCPCB

PCBWay quoted the dev board at $118.65, and then their engineer emailed asking for impedance-control details on the USB pair. The board price itself ($92) felt too expensive for a test board, so I moved the order to JLCPCB. The bare boards there were $5.30, but assembly for the minimum of two boards brought the total to $189.27.

JLCPCB didn't stock the main MCU (an STM32H7S3) or the 74AHC164 shift registers, so I decided to solder those myself. While reviewing the assembly order I also found the component polarities hadn't been set, so from then on I checked every line before paying. The boards arrived on October 20.
