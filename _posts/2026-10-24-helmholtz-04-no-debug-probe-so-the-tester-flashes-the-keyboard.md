---
layout: post
title: "Part 4. No debug probe, so the tester flashes the keyboard"
date: 2026-10-24 12:00:00 +0900
categories: [helmholtz]
tags: [hardware, pcb, firmware, keyboard]
series: "Helmholtz: a keyboard down to the microsecond"
---

*Helmholtz: a keyboard down to the microsecond — part 4 of 7.*

### The tool I didn't have

Board A had no easy way in. Its USB port isn't wired to the chip's built-in bootloader, so the only way to program it is SWD, and the SWD pads are a Tag-Connect footprint: bare pads, no header, exactly as the design called for. I didn't have the Tag-Connect cable yet. Holding probe wires on the pads by hand, the contact slipped in the middle of a flash erase.

So I made the tool out of what I had. Board B already sits next to A with wires running between them. I added an SWD programmer to B's firmware, bit-banging the debug protocol (ARM ADIv5) on B's GPIO pins. Now **B flashes A**, and the PC only has to talk to B.

### First end-to-end run: it failed

That same night, A enumerated as a USB keyboard for the first time: high-speed USB with a 1-microframe polling interval, 8,000 start-of-frame packets per second, exactly 8 kHz. Then I ran the full loop for the first time: B presses a key, A scans, A reports over USB, B timestamps it.

The detection rate was 18% on one test key and 71% on the other, and every report had the wrong key code. The second run the next morning wasn't better: 37 and 46 out of 200. Reports did arrive 10–222 µs after each press, but they contained either an entire row of keys or an "ErrorRollOver" code.

### Turning the ruler into a debugger

"The keyboard is broken" is not a diagnosis. I narrowed it down one step at a time.

1. **A false lead.** A continuity check said the shift-register clock line was shorted to ground. It wasn't. I had measured with the board powered, which makes that reading meaningless.
2. **Count the edges.** I added a simple edge counter to B and pointed it at A's scan clock. It read **134.9 k edges/s, against an expected 136 k** (17 clocks × 8,000 scans). The firmware and the MCU were fine, and this was also the first real measurement showing the scan really runs at 8 kHz.
3. **Slow it down.** A diagnostic build that steps the scan once every 3 seconds made it possible to check each column with a multimeter. Column 0 sat at 0 V no matter what.

The culprit was U5, one of the two 74AHC164 shift registers. The problem went from "A is dead" to "one cheap logic chip is dead." The tool I built to measure speed ended up being the best debugger on the bench.
