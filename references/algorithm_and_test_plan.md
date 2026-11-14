# Trajectory Jitter Algorithm and Test Plan

## 1. Parameter set

Living objects: all 12. Non-living: first 5 only.

| Param | Narrative reading |
|-------|-------------------|
| \(A_{00}\) | Comfort / stability of relationship or use |
| \(A_{01}\) | Origin / how the object or character entered |
| \(A_{02}\) | Motivation / intent |
| \(A_{03}\) | Temperament, thinking style, aggressiveness, passiveness, rigidity or versatility (of objects)|
| \(A_{ 04}\) | Variety of use-cases or relational expressions |
| \(A_{05}\) | Offspring / continuation or manner of ending |
| \(A_{06}\) | Belief system |
| \(A_{07}\) | Social circle and fit |
| \(A_{08}\) | Quirks or imperfections |
| \(A_{09}\) | Stable vision or purpose |
| \(A_{10}\) | Untoward events that hit or brushed past (optional) |
| \(A_{11}\) | Rare higher attributes (optional) |

State intensities are read on a 0–1 scale from the text (0 = minimal/absent, 1 = maximal).

## 2. Residual jitter

For every consecutive pair of contexts and every parameter whose active variable shifted, compute one transition residual:

\[
j_k = \text{raw }\Delta_k \times (1 - q_k)
\]

- raw Δ and q are derived as in "Deriving raw Δ" and "Deriving q".
- A parameter that does not move between two contexts produces no transition. It is not scored and does not enter the aggregate. Parameters that are never mentioned are not transitions either.

Let \(K\) be the number of transitions scored for an object. Report four numbers per object:

| Metric | Definition | Use |
|---|---|---|
| **J_rms** (headline) | \(\sqrt{\tfrac{1}{K}\sum_k j_k^2}\) | Overall residual jitter. Squaring weights large unexplained jumps more than many small ones. |
| **J_peak** | \(\max_k j_k\) | The single worst transition. |
| **Hotspots** | every transition with \(j_k \ge 0.10\) | The transitions to fix, each with its parameter, step, causal type, q and justification. |
| **K and coverage** | \(K\) and \(K/(P \times T)\) (P = parameters used, T = context changes) | Evidence depth. A low K means the headline is weak evidence. |

Also report **J_mean** (the legacy slot-averaged mean: sum of \(j_k\) divided by \(P \times T\), with unmoved slots counting as 0) so results stay comparable with the original ten-biography calibration.

Minimum evidence: if \(K < 8\), mark the J_rms as "low evidence" and do not map it onto an interpretation band.

### Supporting (bridge) nodes
Any node used to raise q is scored with the same four metrics and reported separately. A supporting node's J_rms is never averaged into the object it supports. A node with depth 1 and a hotspot stays visible as a hotspot.


### Deriving raw Δ

raw Δ is the **mean** step size of the change, derived from the variable(s) that actually shifted.

Relevant variables (from VETCESC coordinates):
- \(t\) — time / progression
- \(r\) — radial intensity (desire strength, energy, attachment)
- \(\theta\) — angular orientation (direction of desire or main focus)
- \(\phi\) — azimuthal / wobble (secondary desire, control, deviation)

Rule:
- Gradual or steadily described change on the active variable → small steps (≈ 0.01–0.05).
- Abrupt or unheralded change on that variable → large step.
- raw Δ = mean of those step sizes across the compared contexts.

A continuous multi-year decline in intensity (\(r\)) therefore produces a low mean raw Δ. An unexplained flip in orientation (\(\theta\)) produces a high mean raw Δ.

### Deriving q (causal-inference scale)

q is determined by the directness of the causal link the text (or allowed external source) supplies:

| Type of bridge | q range |
|----------------|---------|
| Direct causal inference (“because X, therefore Y”) | 0.90 – 1.00 |
| One-step indirect | 0.70 – 0.85 |
| Multi-step or weakly linked | 0.40 – 0.65 |
| Speculative or retrospective only | 0.10 – 0.35 |
| No bridge | 0.00 |

Optional decay function:
\[
q = 0.95 \times d^{k}
\]
where \(d\) is the directness factor (1.0 direct, 0.7 one clear step, 0.4 multi-step, 0.1 speculative) and \(k\) is the number of inferential steps.

When reporting a transition the model must state:
1. Causal type
2. Resulting q
3. One-sentence justification from the text
   
## 3. Two-mode rule

- **Pure fiction**: \(q\) may be raised only by material present in the text. External archives are disallowed.
- **Historical or potentially historical**: after scoring on the text, external records may be consulted. Continuous external trajectory raises \(q\); silence or contradiction does not.

Any object used as an explanation must be scored itself; its jitter travels along the DFS edge.

## 4. Graph and DFS

- Build a directed graph of objects and the causal/relational links actually supplied.
- DFS (or longest supporting path) measures depth before a change.
- Shallow depth prevents dilution of a supporting node’s jitter.

## 5. Worked calibration sketches

Status: provisional. Single scorer (Claude), n = 10, conventions fixed before scoring.

## Sources (fixed before scoring)
One English Wikipedia article per figure, first ~100,000 characters read (the fetch limit; this covers the life narrative, not the later legacy sections):
Srinivasa Ramanujan, Marie Curie, Charles Darwin, Abraham Lincoln, Florence Nightingale, Albert Einstein, Mahatma Gandhi, Ada Lovelace, Frederick Douglass, Jane Austen (all `en.wikipedia.org/wiki/<Name>`; Gandhi = `Mahatma_Gandhi`).
Evidence rule: only what the page says. Where the page gives no cause for a change, q = 0.

## Protocol
- Five contexts per life: C1 origin, C2 formative turn, C3 peak work, C4 major adversity, C5 late life and death. Four transitions each.
- A fact sheet per figure was extracted from the page by a summarizing step; I then scored from those sheets.
- A parameter transition is scored only when the sheet reports a change. Parameters used: A00 comfort, A02 motivation, A03 temperament, A05 ending (death only), A06 beliefs, A07 social circle, A08 quirks, A09 purpose, A10 untoward events. A01, A04 and A11 were not extracted.

## Conventions (the skill does not quantify "large")
- Raw Δ: gradual 0.05; abrupt change in intensity or wobble 0.40; orientation flip 0.60; ending 0.80.
- q: direct 0.90; one-step 0.75; multi-step or weak 0.50; speculative 0.25; page silent 0.00.
- A10 (untoward events): q reflects whether the event is clearly stated (0.90), not why it happened, as in the Caesar run.
- Death: assassination with a stated shooter 0.90; death from a named illness or old age 0.75; disputed diagnosis 0.50.

## 6. Calibration on 10 biographies (historical mode)


| Figure | K | J_rms | J_peak | J_mean (legacy, 48 slots) | Hotspots (≥0.10) |
|---|---|---|---|---|---|
| Ramanujan | 13 | 0.065 | 0.200 | 0.012 | 1 |
| Curie | 19 | 0.057 | 0.200 | 0.013 | 2 |
| Darwin | 19 | 0.059 | 0.200 | 0.016 | 2 |
| Lincoln | 18 | 0.045 | 0.100 | 0.011 | 2 |
| Nightingale | 11 | **0.166** | 0.400 | 0.026 | 3 |
| Einstein | 16 | 0.063 | 0.200 | 0.015 | 1 |
| Gandhi | 18 | 0.038 | 0.080 | 0.012 | 0 |
| Lovelace | 15 | 0.062 | 0.200 | 0.011 | 2 |
| Douglass | 14 | 0.067 | 0.200 | 0.014 | 2 |
| Austen | 16 | **0.105** | 0.400 | 0.018 | 1 |
| **Mean ± SD** | | **0.073 ± 0.037** | 0.218 ± 0.106 | 0.015 ± 0.004 | |
| Caesar (play, different protocol) | 20 | 0.088 | 0.225 | 0.028 | 6 |

Provisional J_rms bands (mean + 1, 2, 3 SD): ≤ 0.11 smooth; 0.11–0.15 moderate; 0.15–0.19 noticeable gaps; > 0.19 high.

## What the data say
1. **The old 0.050 anchor does not reproduce.** The legacy 48-slot mean here is 0.015 (range 0.011–0.026), about a third of 0.050. J_rms (0.073) lands nearer to it only because it divides by scored transitions, not slots.
2. **No discrimination from one fiction case.** Caesar's J_rms (0.088) falls inside the biography range and below the 0.11 cutoff. The metric did not separate this fiction from the biographies; it measures continuity with explicit bridges, not truthfulness.
3. **The peak metric is mostly a death-convention artifact.** In 7 of 10 lives the peak is the same ending transition (0.80 × 0.25 = 0.20). Hotspots at 0.10 are largely the same artifact. Fix: score a terminal death outside the peak and hotspot list, or give a stated cause q 0.90.
4. **Two real outliers have real reasons.** Nightingale (0.166): the page gives no cause for her 1857 onset of chronic illness and depression (A03 0.40, q 0). Austen (0.105): a disputed diagnosis at death plus one large financial break.
5. **The bands are tied to the conventions.** Moving the raw Δ scale (×0.67 to ×1.5) and q shift (−0.10 to +0.05) moves the set mean from 0.041 to 0.141. Publish the numeric conventions with the bands or the bands will not transfer.
6. **Page-only evidence is not the main driver.** Only 16 of 159 scored transitions had a "page silent" cause. Giving those a weak bridge (q 0.50) lowers the mean only from 0.073 to 0.068.

## Limitations
- One scorer, no inter-rater check; the fact sheets themselves involve a summarizing step.
- First ~100,000 characters of each page; legacy sections unread.
- A01, A04, A11 not scored; A05 only at death (9 parameters effective).
- n = 10, and Nightingale alone raises the SD to 0.037.
- Wikipedia is a secondary source; its smoothness reflects editors' narrative choices.

## Scored entries (transition, parameter, raw code, q code)
Raw: G gradual 0.05, A abrupt 0.40, F flip 0.60, E ending 0.80. q: D 0.90, O 0.75, M 0.50, S 0.25, N 0.00.

```
Ramanujan  1:A00 A D, A07 G O, A10 A D, A09 G O | 2:A00 A D, A07 A D, A09 G D, A06 F D | 3:A00 G M, A10 A D | 4:A00 A D, A07 A D, A05 E O
Curie      1:A00 G D, A02 G D, A07 G D, A09 G D | 2:A00 G D, A07 G D, A02 G D, A09 G D | 3:A00 A D, A07 A D, A03 A O, A02 A D, A09 A D, A10 A D | 4:A00 G O, A02 A D, A09 A D, A07 G D, A05 E O
Darwin     1:A00 A D, A02 G D, A07 A D, A09 F D | 2:A00 G O, A02 G D, A06 G M, A09 G D, A07 G O, A10 A O | 3:A00 A D, A02 A D, A07 A D, A10 A D | 4:A00 G O, A07 G O, A09 G D, A06 G N, A05 E O
Lincoln    1:A00 A O, A02 G N, A07 A O, A09 G N | 2:A00 G D, A02 G D, A06 G D, A07 G D, A09 G D | 3:A00 G D, A02 G D, A07 G D, A10 A D | 4:A00 A D, A02 G D, A06 G D, A10 A D, A05 E D
Nightingale 1:A02 G N, A07 G D | 2:A00 A D, A07 A D, A09 A D | 3:A00 A S, A03 A N, A09 G N | 4:A00 G N, A09 G N, A05 E O
Einstein   1:A00 A D, A07 G O, A02 G N | 2:A00 G D, A02 G N, A07 G D | 3:A00 A D, A06 A D, A09 F D, A07 A D, A10 A D | 4:A00 G D, A09 G N, A06 G N, A03 G N, A05 E O
Gandhi     1:A00 A D, A06 G D, A07 A D, A09 A D, A03 G O | 2:A02 G D, A06 G D, A07 G D, A03 G D, A00 G O | 3:A00 A D, A07 A D, A02 F D, A10 A D | 4:A00 A D, A02 A D, A08 G N, A05 E D
Lovelace   1:A02 G D, A07 G D, A00 G O | 2:A00 A D, A09 G D, A10 A D | 3:A00 A D, A07 G O, A02 G O, A08 G S | 4:A00 A O, A07 G D, A06 G D, A09 G D, A05 E O
Douglass   1:A00 A D, A07 A D, A10 A D | 2:A02 G D, A09 G D, A07 G D, A06 G M, A00 A D | 3:A00 A O, A10 A D | 4:A00 A D, A09 A D, A02 A D, A05 E O
Austen     1:A07 G D, A00 A D, A10 A D | 2:A00 A D, A07 A D, A02 G M, A09 G M, A10 A D | 3:A00 A D, A03 G N, A02 G O, A10 A D | 4:A00 G D, A02 G O, A03 G N, A05 E M
```

## 7. Test-plan passages for further checks

1. Additional biography excerpts to enlarge the calibration set.  
2. Animal Farm objects already scored.  
3. Known loophole scenes (absolute rule later violated with no prior mechanism).  
4. Clean non-living object with single stable use.

## 8. Reporting template

- Object | living? | parameters used | residual jitter | key supporting nodes and their jitter  
- Overall mean and any high-jitter subgraphs  
- Mode used (fiction vs historical) and whether external sources altered any \(q\)  
- Comparison to calibration mean (0.050) and bands above.
