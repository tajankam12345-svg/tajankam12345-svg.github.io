---
layout: post
title: "Part 2. Real boards broke my first AI"
date: 2026-11-10 10:00:00 +0900
categories: [pcb]
tags: [simulation, pcb, machine-learning]
series: "Testing circuit boards without building them"
---

*Testing circuit boards without building them — part 2 of 6.*

Working on a demo board I designed myself proves nothing. So I fed it real boards designed by other people.

### Reading KiCad files directly

I wrote a parser for KiCad, the free PCB design tool a lot of hobbyists and startups use. It reads the outline, layer stackup, components, pads, traces, vias and copper pours. I ran an open-source board through it (4 layers, 101.6×53.3 mm, 109 components, 211 vias), and all 10 checks finished in 50 seconds.

Then I hit the first wall. **Design files are missing most of the values physics needs.** How many watts a chip burns, how much current flows on a power rail, which nets carry fast signals: none of it is in the file. So the tool estimates those values from part names and net names, and every estimate gets listed in the report. If you know the real value, you can override it.

If you quietly fill unknowns with plausible guesses, the result can look right and still be untrustworthy. So every result shows exactly what assumptions it was computed under.

### An AI trained on 263 boards

Next I wanted to train the AI on real boards. I collected 263 KiCad boards from 236 open-source GitHub repos, varied the heat loads to create 890 scenarios, and used the solver to generate ground-truth heat maps.

It failed. On 60 boards it had never seen, the median error on hotspot temperature was 66%. The error across the whole heat map was 39% of the peak temperature. Not usable.

The reason was simple. Every board is wildly different, and 263 is nowhere near enough to learn that variety. The AI didn't learn physics. It memorized 263 boards.

That left me with two questions. Where do I get far more data? And who computes the right answers for that data?
