---
layout: post
title: "Part 6. Going after the weak spots first"
date: 2026-11-12 14:00:00 +0900
categories: [pcb]
tags: [simulation, pcb, machine-learning]
series: "Testing circuit boards without building them"
---

*Testing circuit boards without building them — part 6 of 6.*

I decided to try everything I could. My PC ran day and night, and I kept the GPU busy whenever I wasn't using the computer and while I slept.

I also changed strategy. Instead of polishing what already worked, I picked the checks that were **partial, missing, or had high error** and started collecting data and training on those first.

### 3D electromagnetics, side by side with the expensive tools

The fields around a via (the plated hole connecting layers) or a capacitor can't be captured by a 2D calculation. So I took openEMS, an open-source 3D electromagnetic solver, and built the board structures that matter in practice myself.

| Check | Compared against | Error |
| --- | --- | --- |
| Capacitor mounting inductance (predicting bottom-side mounting) | Published VNA measurements, 8-layer board | 4.7% |
| Signal through a via, S21 (0–20 GHz, 6 configurations) | Published results from CST, a commercial tool | 1.9% (worst 3.1%) |
| Power-plane resonance | Cross-check against my cavity model | 1.2% |

When the via results matched CST within 0.16 dB on average, it was one of the best moments of this whole project. Free software and my own code gave the same answer as an expensive commercial tool.

### Getting to that number

The path there was full of bugs.

- The signal came out **stronger** after passing through the via (S21 > 0 dB). Energy doesn't appear from nowhere. The measurement plane was sitting behind the point where the signal was injected.
- The bottom copper plane vanished entirely. A line that should have been at height 0 ended up as 0.0000000000000002 inside the computer.
- Dozens of results all came back with S21 = 0. Ultra-thin cells between grid lines forced tiny time steps, so the runs stopped before they finished.

That last bug hurt, because I had already trained a via AI on that data. Trained on 55 samples, it was off by 16.9 dB, which is useless. I threw out all the data, confirmed the fixed generator passed the CST benchmark again, and started over. With 176 clean samples (zero bad results), I retrained. On 26 test vias it had never seen, the error dropped from 16.9 dB to 0.20 dB on average (2.3% in magnitude, 3.7° in phase) from 0 to 20 GHz. Same model, same training code. The only thing that changed was the quality of the answers it learned from.

I relearned the lesson from Part 3, painfully. An AI learns its training answers exactly as given. If the answers are wrong, it learns the wrong thing very diligently. Since then, before generating data at scale, I verify the generator against an independent reference like CST first.

### Still unsolved: air over a hot board

My airflow solver matches standard benchmarks within 0.1%. But on the question I actually care about, how fast the top of a hot board cools in still air, it comes out about 40% lower than the textbook correlation.

I tried a 1.5× finer grid. The value moved only 5%, so the grid isn't the problem. Now I'm testing separately whether the simulation box is too small or the airflow speed setting is off. I'm not going to multiply by a fudge factor without knowing the cause. That would make the number match while leaving the solver just as wrong.

### The weak links that remain

- Thermal resistance for poorly documented parts: 36% error
- Missed problems in real fix histories: 46%
- Radiated EMI and antennas: almost no published measurements. I'll have to digitize graphs from papers.
- High-speed link eye diagrams: not built yet

Three days ago all I had was a heat map. Now I'm benchmarking 3D electromagnetics against commercial tools. But the further I go, the clearer one thing gets. To imitate reality, you ultimately need data from reality. Every weak link left isn't where the math is hard. It's where there's no real measurement to compare against.
