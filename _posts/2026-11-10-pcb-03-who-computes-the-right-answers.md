---
layout: post
title: "Part 3. Who computes the right answers?"
date: 2026-11-10 11:00:00 +0900
categories: [pcb]
tags: [simulation, pcb, machine-learning]
series: "Testing circuit boards without building them"
---

*Testing circuit boards without building them — part 3 of 6.*

While thinking through how to train the AI, I asked myself a basic question. An AI learns by looking at right answers. So who computes those answers?

The answer is the physics solver from Part 1. It's slow, but it follows the laws of physics. The AI looks at tens of thousands of answers the solver produced and learns to produce the same answers much faster. That means the ceiling on accuracy in this setup is the solver, not the AI. Once that clicked, the work split into two tracks:

- Make the solver more accurate (Part 4)
- Use the solver to produce far more answers (this post)

### Generating the boards myself

Part 2's problem was having only 263 boards. So I generated boards: random component placement, heat loads, copper pours, vias and layer counts, tens of thousands of them, each solved by the solver.

I ran this on my desktop's GPU. I wrote batch files that handle everything from generation to training with a double-click, and let them run overnight.

| Round | Training boards | Grid | Hotspot error (median) | Inference time |
| --- | --- | --- | --- | --- |
| 1 | 11,268 | 128×128 | 1.89% | 2.6 ms |
| 2 | 33,673 | 128×128 | 1.16% | 4.2 ms |
| 3 (complex boards) | 9,502 | 256×256, up to 8 layers | 2.57% | 5.1 ms |
| Voltage-drop AI, round 1 | 8,062 | 256×256 | 14.9% | — |

The error that was 66% in Part 2 dropped to 1–3%. What takes the solver seconds to minutes, the AI does in a few milliseconds. Tripling the data (round 1 → round 2) cut the error again.

Next I wanted it to handle more complex circuits. Round 3 doubled the grid resolution and went up to 8 layers. Even with fewer boards, it held at 2.57%. The voltage-drop AI, on the other hand, was off by almost 15%. The same recipe doesn't work equally well for every kind of physics.

### But 1% of what?

I was happy with the numbers for about a minute before something felt off. That 1% measures **how closely the AI matches my solver**, not **how closely it matches reality**. If the solver is 30% off from the real world, the AI will faithfully reproduce that 30% with 1% error.

So the next question was obvious. How far is the solver from real measurements?
