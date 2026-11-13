---
layout: post
title: "Part 4. Solver vs. reality, and where the gap comes from"
date: 2026-11-11 12:00:00 +0900
categories: [pcb]
tags: [simulation, pcb, machine-learning]
series: "Testing circuit boards without building them"
---

*Testing circuit boards without building them — part 4 of 6.*

To compare the solver against reality, you need measurements. I don't own any lab equipment, so I went looking for published ones: thermal-resistance measurements that chip makers put in datasheets and application notes, and trace measurements published in papers and open-source repos. I used an AI agent to collect that scattered material, sort it by test conditions, and apply the same calculation hundreds of times. Then I ran every case under its own conditions and compared.

| Check | Samples | Mean error |
| --- | --- | --- |
| Board thermal resistance θJA, well-documented test conditions | 84 | 3.5% |
| Board thermal resistance θJA, poorly documented test conditions | 20 | 36.1% |
| Trace impedance, full test conditions given | 4 | 1.7% |
| Trace impedance, stackup had to be assumed | 8 | 19.0% |
| Trace loss, 0.3–9 GHz | 7 | 5.6% |
| Crosstalk, near end (NEXT) | 8 | 34.8% |
| Crosstalk, far end (FEXT) | 8 | 27.2% |

### What the numbers said

Side by side, a pattern jumped out. **The same solver matched well when the test conditions were documented and missed badly when they weren't.** Thermal resistance: 3.5% vs. 36%. Impedance: 1.7% vs. 19%.

A lot of the error doesn't come from bad physics. It comes from **not knowing the inputs**. If you don't know the board thickness, the copper weight or the airflow, even a great solver has to fill those in with assumptions. Listing every estimate in the report back in Part 2 paid off here. You have to know what's assumed to know what to doubt.

Crosstalk is a different story. The same measurements had also been run through Ansys SIwave, a commercial tool, and those results were published too. SIwave was off by 12.3% on NEXT and 104.7% on FEXT. My solver lost on one and won on the other. Even expensive tools don't nail reality. Crosstalk is extremely sensitive to tiny spacing differences, so the measurements themselves carry a lot of uncertainty.

### Not hiding the accuracy

From here on I tracked every check in an accuracy table: what it was compared against, how many samples, and the error percentage. Showing only the checks that match well would look better, but then whoever uses this tool later wouldn't know how far to trust it. Checks that miss get labeled "weak."

I tried one more test. GitHub keeps real fix histories, commits like "this part ran too hot, so I fixed it." Feed in the design from before the fix and see whether the checker flags the problem. It caught 7 of 13 pairs. Most of the 6 it missed were fixes that only added vias. There's still a long way to go.
