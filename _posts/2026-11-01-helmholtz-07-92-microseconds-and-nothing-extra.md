---
layout: post
title: "Part 7. 92 microseconds, and nothing extra"
date: 2026-11-01 15:00:00 +0900
categories: [helmholtz]
tags: [hardware, pcb, firmware, keyboard]
series: "Helmholtz: a keyboard down to the microsecond"
---

*Helmholtz: a keyboard down to the microsecond — part 7 of 7.*

### The first real number

After replacing the dead shift register, B re-flashed A, and column 0 came back: 1,000 out of 1,000 presses detected. The rework had also cost me something. ROW2's pull-up resistor (R8) was missing, which made row 2 float and throw ghost keys. Another thing to solder.

Then I ran the latency test I'd been building toward for four weeks: 1,000 presses each on two keys.

| Key | Mean | Std. dev. | Min | p50 | p90 | p99 | Max |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ESC | 92.3 µs | 35.3 | 31.2 | 91.5 | 140.6 | 154.9 | 155.9 |
| S | 90.4 µs | 35.3 | 28.4 | 89.4 | 139.7 | 151.3 | 153.2 |

This is the dev board running the first firmware (a scan synced to each 8 kHz USB frame), not the final board. The design had predicted an 85 µs average. Measured: 90–92 µs.

The standard deviation says even more than the mean. 35.3 µs is almost exactly 125/√12, the standard deviation of a uniform distribution 125 µs wide. In other words, nearly all the spread comes from **where in the 125 µs USB polling cycle the key happens to be pressed**. The keyboard itself is consistent. The randomness is USB's.

### The next wall is USB itself

That raised the obvious question: can you poll faster than 8 kHz? For a USB interrupt endpoint, 125 µs is the floor. Going below it would mean a bulk endpoint plus resident software on the PC, theoretically 10–30 µs but with OS jitter and a real risk of tripping game anti-cheat systems. The transposed-matrix firmware for the final board predicts about 70.7 µs, and its theoretical floor is about 66 µs, because an average 62.5 µs of that is just waiting for the next poll.

### Restraint, all the way down

On November 1 I went through the production board one more time and asked what each part was really for.

- **Removed**: the reset button, the UART test pads, the status LED and its resistor.
- **Replaced**: the BOOT button became two bare pads. To enter the bootloader, you bridge them with tweezers while plugging in USB.
- **Kept**: three fiducials for assembly, two test points for watching the scan waveform, a ground point, the Tag-Connect pads and the vibration sensor.

The rule from Part 2 (nothing visible from the outside) now applies on the inside too. Then I deleted every trace. The board is now a clean base: 261 parts, 0 tracks, planes and rule areas in place. I'm routing it by hand.

### Where it stands

A_PROD2 hasn't been ordered yet. Next up: sending MADPCB the seven questions and placing the order, the first boot of the production board, the first latency measurement with the new firmware (predicted 70.7 µs), and measuring the board's vibration to choose the TMD mass. I'll write about each of those when they happen, with real numbers.
