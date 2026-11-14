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

\[
\text{jitter}_{i \to i+1} = |\text{state}_{i+1} - \text{state}_i| \times (1 - q)
\]

\(q \in [0,1]\) = quality of the narrative (or allowed external) explanation that bridges the two contexts.

Parameter jitter = mean of the residuals.  
Object jitter = mean of its parameter jitters.  
Unmentioned but non-contradictory parameters = 0.

## 3. Two-mode rule

- **Pure fiction**: \(q\) may be raised only by material present in the text. External archives are disallowed.
- **Historical or potentially historical**: after scoring on the text, external records may be consulted. Continuous external trajectory raises \(q\); silence or contradiction does not.

Any object used as an explanation must be scored itself; its jitter travels along the DFS edge.

## 4. Graph and DFS

- Build a directed graph of objects and the causal/relational links actually supplied.
- DFS (or longest supporting path) measures depth before a change.
- Shallow depth prevents dilution of a supporting node’s jitter.

## 5. Worked calibration sketches

### Ramanujan (historical mode — short passage)
Passage supplies continuous illness → work → death.  
External biographical record confirms multi-year hepatic trajectory.  
\(q \approx 0.9\) on \(A_{01}\), \(A_{05}\), \(A_{09}\).  
Residual jitter ≈ 0.02–0.05.

### Napoleon (Animal Farm — pure fiction)
Raw jumps exist, but most are bridged inside the text (\(q\) 0.65–0.90).  
Personal residual jitter ≈ 0.05.  
Supporting dogs node (DFS depth 1, almost no training shown) retains residual jitter ≈ 0.54.  
Subgraph therefore higher than Napoleon alone.

### Seven Commandments (non-living, pure fiction)
Repeated night-time rewrites with weak bridges.  
Residual jitter on \(A_{00}\), \(A_{04}\) ≈ 0.50–0.65.

## 6. Calibration on 10 biographies (historical mode)

Object-level mean residual jitter:

| Figure | Jitter |
|--------|--------|
| Ramanujan | 0.034 |
| Marie Curie | 0.04 |
| Charles Darwin | 0.05 |
| Abraham Lincoln | 0.06 |
| Florence Nightingale | 0.045 |
| Albert Einstein | 0.055 |
| Mahatma Gandhi | 0.07 |
| Ada Lovelace | 0.05 |
| Frederick Douglass | 0.06 |
| Jane Austen | 0.04 |

- Mean of means: **0.050**
- Range: 0.034–0.07
- Approx. SD: 0.012

**Reference bands**  
- ≤ 0.08 : biography-like / very smooth  
- 0.08–0.15 : moderate, still largely continuous  
- 0.15–0.30 : noticeable gaps or retrospective smoothing  
- > 0.30 : high residual jitter  

These bands supersede earlier provisional thresholds. The set is small and judgment-based but supplies a concrete empirical anchor.

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
