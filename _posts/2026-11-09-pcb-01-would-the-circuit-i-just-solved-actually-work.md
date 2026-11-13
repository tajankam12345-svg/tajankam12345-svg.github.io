---
layout: post
title: "Part 1. Would the circuit I just solved actually work?"
date: 2026-11-09 09:00:00 +0900
categories: [pcb]
tags: [simulation, pcb, machine-learning]
series: "Testing circuit boards without building them"
---

*Testing circuit boards without building them — part 1 of 6.*

In my circuits class (ESE 271) we solve RLC circuits, phasors, impedances. Halfway through a practice exam, a question popped into my head. If I actually built this circuit on a real board, would it work the way my answer says?

Circuits in class are idealized. Wires have no resistance, parts never heat up, and the air around them might as well not exist. Real boards aren't like that. Copper traces heat up and their resistance rises. Fast signals bleed noise into neighboring traces. How air moves inside the enclosure changes how long parts last. Usually you only find out about those gaps after you've built the board, once the time and money are already spent.

My previous project had taught me the same thing: a test isn't done until it's done in the real world. So what if I built a virtual test that imitates the real world closely enough?

### Why an AI and not just a solver

Honestly, I can't build a perfect solver. I'm a sophomore, and I'm not a math major. Solving heat, electromagnetic fields and airflow exactly is something specialists have spent decades on.

And even a perfect solver would be slow. Every time the ambient temperature, humidity, airflow speed or room size changes, you have to solve the whole thing again from scratch. Nobody can wait hours every time they tweak a design.

So I set the direction like this:

1. First, build a solver I can trust even if it's slow, and check it against problems solvable by hand and against published measurements.
2. Use that solver to precompute answers for a huge number of cases.
3. Train an AI on those answers, so it can answer a new design in seconds.

The goal: feed in a design file and, without building anything, learn things like "this part will run hot" or "this trace will pick up noise."

### What I built the first night

I started with just two things: heat and voltage drop. The solver splits each layer of the board into a 0.5 mm grid and stores how much of each cell is covered by copper, so even traces thinner than a cell are computed at their real width. Hot copper has more resistance, and more resistance makes more heat, so the solver iterates the two until they agree.

The first thing I did after building it was compare it to problems I could solve by hand.

| Case | Compared against | Result |
| --- | --- | --- |
| Voltage drop along a straight copper strip | I·ρ·L/(W·t) | Exact match |
| Point heat source on a wide copper plane | Bessel-function analytic solution | 0.3–1% error |
| Trace thinner than a grid cell | Same formula | Within 3% |
| Grid refined from 1 mm to 0.125 mm | Convergence | Within 0.3 °C |

Next I added a demo board (75×60 mm, 4 layers, an STM32H7 microcontroller and a motor driver) and trained my first AI, a U-Net, on answers from the solver. By early morning I had expanded it to 10 checks: heat, voltage drop, trace current, component lifetime, clearance, signal integrity, power noise, EMI, antenna behavior, and board warpage. I called it a "physics checkup" for boards. The 2D field solver for trace impedance matched textbook formulas within 1%.

Getting this far in one night gave me a lot of confidence. The problem was that all of it only ran on a demo board I had made up myself.
