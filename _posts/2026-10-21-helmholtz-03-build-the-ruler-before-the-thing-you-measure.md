---
layout: post
title: "Part 3. Build the ruler before the thing you measure"
date: 2026-10-21 11:00:00 +0900
categories: [helmholtz]
tags: [hardware, pcb, firmware, keyboard]
series: "Helmholtz: a keyboard down to the microsecond"
---

*Helmholtz: a keyboard down to the microsecond — part 3 of 7.*

### First boot, 90 mV short

The day the boards arrived, I brought up the tester side (B) first. Hold BOOT, plug in USB, and the chip's ROM bootloader shows up. Flash over DFU, and the board enumerates as a serial port. It was the first time real hardware talked back.

Then the surprises started.

- The PLL configuration had the VCO running at 1200 MHz, outside the datasheet range. Fixed.
- The core voltage measured 1.265 V and 1.272 V on the two boards, about **90 mV below typical**. The question was whether to keep running at 600 MHz. I looked at the margins in the datasheet and decided to keep 600 MHz, logging it as a known, accepted risk.
- At boot, the flash ECC error flag kept getting set. It took three days to trace: reading the chip's unique ID was pulling a whole D-cache line from an unwritten flash address. A memory-protection (MPU) region fixed it.

None of these were dramatic, but they set the tone. When something is off, put a number on it, then decide.

### Defining "latency" before measuring it

The point of this project is to say how fast the keyboard is, so the first job was to define "fast" precisely.

**Latency = from the instant B shorts the switch contacts to the instant B's USB host receives the HID report.**

B closes the "switch" with an analog switch (no mechanical bounce), and that edge comes from a hardware timer's one-shot pulse. A second timer timestamps everything, and the USB receive interrupt reads that timer directly when the report lands. Before trusting the tester, I worked out its own error budget: worst case about **0.3 µs**. A ruler that's off by more than the thing you're measuring is useless.

I also didn't want to wait for the keyboard to test the tester. So I ran it against a simulated keyboard first: 10,000 records, zero dropped, a mean around 96 µs. The tester passed its gate 14 days ahead of schedule.

### Why QMK waited

Most custom keyboards run QMK, the open-source firmware, and I had it ported. But I put it on hold. To validate a new measurement tool, you need a device under test whose behavior you fully understand, and a minimal HID keyboard is far easier to reason about than a full QMK build. Digging into QMK on this chip also turned up two bugs in the original port: `micros()` ran at half speed, and a timer interrupt was never enabled.

That same day I hand-soldered the keyboard side: the main MCU and two 74AHC164 shift registers.
