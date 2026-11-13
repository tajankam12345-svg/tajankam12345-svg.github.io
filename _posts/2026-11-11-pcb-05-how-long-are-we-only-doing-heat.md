---
layout: post
title: "Part 5. How long are we only doing heat?"
date: 2026-11-11 13:00:00 +0900
categories: [pcb]
tags: [simulation, pcb, machine-learning]
series: "Testing circuit boards without building them"
---

*Testing circuit boards without building them — part 5 of 6.*

Once the thermal AI was down in the 1% range, I got impatient. Boards don't fail only because of heat. Signals degrade, power rails get noisy, solder joints crack under vibration, products fail EMC certification. Nailing heat alone gets me nowhere near "test a board without building it."

### What do the pros simulate?

So I looked at what the industry's commercial tools support. Products from Ansys, Cadence, Keysight and Siemens are split by domain: thermal, airflow, power, signal, 3D electromagnetics, EMI, circuits, mechanical. Each is sold separately, and none are priced for a student.

Doing that research made my goal bigger.

### An Adobe for engineering

I thought about Adobe. Photoshop, Illustrator and Premiere each work on their own, but files move between them naturally. Why couldn't physics simulators work the same way?

- Build a **module** for each physical phenomenon: heat, electrical, airflow, vibration. Each one can be used on its own.
- Modules share the same design data and pass results to each other. For example, the airflow module computes how fast each surface cools, the thermal module takes that in, and the electrical module takes the resulting temperatures.
- The user sets **environment variables** like ambient temperature, humidity, airflow and room size.
- Every module has a fast AI version and a slower, validated solver version.

Circuit boards are just the first application.

### What I added in one day

Using the commercial tools' feature lists as a checklist, I filled the gaps one at a time. Everything new was checked against analytic solutions or published measurements, and the numbers are below.

| Added | Compared against | Error |
| --- | --- | --- |
| Circuit simulation (SPICE, reads schematics) | Hand calculation, a real failure case | Exact match |
| Chip signal models (IBIS) | 826 public files from GitHub | 4.7% |
| Power-plane resonance | Published measurements | 6.5% |
| Natural vibration frequencies | 15 published vibration tests | 7.0% |
| Solder-joint thermal fatigue life | Published temperature-cycling tests | 67.8% → 19.1% after calibration |
| Airflow (lattice Boltzmann CFD) | Standard natural-convection benchmarks | 0.08% (2D), 1.7% (3D) |

Solder fatigue was 68% off at first. I fit the constants on one part size (2512) and had it predict another (1206), and it came in at 19%. Keep the data you fit on separate from the data you evaluate on. I learned that one the hard way, too.

By the end of the day, against the commercial tools' 18 categories, 13 were supported, 4 were partial (board cooling by airflow, 3D electromagnetics, radiated EMI, antennas), and 1 was missing (high-speed link eye diagrams).

And I made up my mind. Do everything I can. The goal is to support every one of these with high accuracy.
