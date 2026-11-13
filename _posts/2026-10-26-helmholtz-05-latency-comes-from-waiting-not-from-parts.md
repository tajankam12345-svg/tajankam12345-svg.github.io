---
layout: post
title: "Part 5. Latency comes from waiting, not from parts"
date: 2026-10-26 13:00:00 +0900
categories: [helmholtz]
tags: [hardware, pcb, firmware, keyboard]
series: "Helmholtz: a keyboard down to the microsecond"
---

*Helmholtz: a keyboard down to the microsecond — part 5 of 7.*

The dead shift register made me reopen the whole latency design. If I was going to change parts anyway, which changes would actually make the keyboard faster?

### Where the microseconds go

I broke the estimated latency (about 120 µs on average at the time) into pieces. Nearly all of it wasn't in the components at all. It was **waiting**:

- Waiting for the scan to come around to the key that was pressed.
- Waiting for the next USB poll. At 8 kHz the host asks every 125 µs, so on average a finished report sits for 62.5 µs before it can leave.

The parts themselves (pull-up resistors, shift-register speed, series resistors) were worth a few microseconds at most.

### A decision I reversed the next day

There was a parts-swap proposal on the table: stronger 2.2 kΩ pull-ups, a faster 74LV164, 33 Ω series resistors. I said "let's go with option 1." The same day, a closer look at the firmware timing showed the real gain would be only 5–7 µs. The next morning I withdrew it. Changing your mind is cheap before the boards are ordered.

### The day the design changed

October 27 turned into a chain of decisions:

1. **Remove the shift registers.** Drive all 16 columns straight from GPIO pins and scan continuously with DMA. The part that failed in Part 4 is simply gone. Instead of fixing a weak part, design it out.
2. **Transpose the key matrix.** Drive the 6 rows and sense the 16 columns, instead of the other way around. All 91 diodes flip polarity, and one scan drops from 16 steps to 7. Predicted average latency: **70.7 µs**. One catch: a row pin now sinks up to 12.6 mA when 16 keys are held, above the recommended spec but within the 20 mA absolute maximum. I signed off on that knowingly.
3. **Build only board A.** The standalone tester board and the A+B production panel were canceled. The dev board already has a working tester.
4. **Pick the fab for the look.** I went with MADPCB, the only vendor I found that explicitly listed matte white solder mask, and confirmed they could do a black core.
5. **No hidden header either.** A tucked-away 2×5 SWD header was dropped in favor of the same Tag-Connect pads.

The stackup changed that day too. In one day the prototype became a different product. It's simpler, has fewer parts, and should be faster.
