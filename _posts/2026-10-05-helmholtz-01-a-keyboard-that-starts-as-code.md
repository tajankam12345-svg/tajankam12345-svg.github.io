---
layout: post
title: "Part 1. A keyboard that starts as code"
date: 2026-10-05 09:00:00 +0900
categories: [helmholtz]
tags: [hardware, pcb, firmware, keyboard]
series: "Helmholtz: a keyboard down to the microsecond"
---

*Helmholtz: a keyboard down to the microsecond — part 1 of 7.*

Helmholtz is a mechanical keyboard I'm designing to sit at the premium end of the market: an 88-key tenkeyless (TKL) board with an 8 kHz polling rate. I wanted two things that most keyboards never measure.

- **Latency**: the time from a switch closing to the USB report leaving the keyboard. If you're going to claim "fast," you should be able to prove it in microseconds.
- **Vibration**: eventually the PCB will be coupled to a tuned mass damper (TMD), a small mass tuned to soak up the board's strongest resonance. To size that mass, I first need to measure the board's natural frequencies, so the board carries a small vibration sensor.

The first board isn't the product. It's a test board for latency testing, error testing, firmware development and vibration measurement.

### From Altium to KiCad, and into code

The original schematic was in Altium. I moved it to KiCad 10, but instead of drawing the board by hand, I set it up so the whole design lives in one Python file. Every part, pin and net is written there, and scripts generate the rest: schematic, placement, routing (through the Freerouting autorouter) and fabrication files.

```
design.py → gen_sch → gen_pcb → route → fab
```

The appeal is that a design change is a diff, not an afternoon of clicking. Change one line, regenerate everything, and re-run the checks.

### One board, many layouts

Enthusiast keyboards have to support different bottom rows and split keys. I wanted one PCB to take a 7u spacebar row (1.5 / 1 / 1.5 / 7 / 1.5 / 1 / 1.5) as well as the standard 6.25u row, a split backspace, a split right shift and a stepped Caps Lock. That adds 14 alternate switch positions on top of the 88 keys, and their holes have to coexist.

There was a clever-looking option that kept every hole separate. I checked how major keyboard PCBs actually handle it, and almost all of them just let the alternate positions overlap. I went with the conventional approach. Clever isn't worth much if it's harder to manufacture.

### The first audit: NO-GO

Before ordering anything, the design went through an independent audit. The verdict was **NO-GO**:

- A 4.0 mm non-plated hole for one alternate switch overlapped another key's plated hole by **0.034 mm**. That's less than the thickness of a sheet of paper, but a fab house will still flag it.
- The design changed during the audit, so the fabrication files were stale.

I didn't mind. Catching a 0.034 mm problem before paying for boards is exactly what an audit is for. It also set the habit for the rest of the project: nothing counts as done until something independent has checked it.
