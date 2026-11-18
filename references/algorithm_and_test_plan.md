# Trajectory Jitter Algorithm and Test Plan

## 1. Parameter set

Living objects: up to 12 (max). Non-living: first 5 only.

| Param | Narrative reading |
|-------|-------------------|
| \(A_{00}\) | Comfort / stability of relationship or use |
| \(A_{01}\) | Origin / how the object or character entered |
| \(A_{02}\) | Motivation / intent |
| \(A_{03}\) | Temperament, thinking style, aggressiveness, passiveness, rigidity or versatility (of objects)|
| \(A_{04}\) | Variety of use-cases or relational expressions |
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

### Plot-level jitter
A plot-level score combines every node in the graph. Per-object scores are never averaged into each other; the plot score pools their transitions.

- **Node set:** every node DFS reaches from the focal objects, down to a stated depth, plus every node used to raise a q. Report the depth used. Nodes with no scored transitions (K = 0) are listed but do not contribute.
- **J_plot (headline)** = √( Σ over all nodes and transitions of j² / K_total ). This equals a K-weighted RMS of the node J_rms values, so a node with 20 transitions counts more than one with 1.
- **Also report:** the worst node's J_rms, the combined hotspot list (j ≥ 0.10, tagged by node), K_total, and the number of nodes with K < 8.
- **Evidence rule:** a node with K < 8 is reported but flagged "low evidence." If K_total < 8, mark J_plot "low evidence."
- **Not visible to J_plot:** a missing causal link between nodes, or an event no node carries, produces no transition. Flag any such gap by hand.
- **Bands:** the biography bands may not strictly transfer to J_plot. Plot-level bands need a calibration set with known casts.

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
- **Lookup trigger**: external records are consulted only for transitions that remain hotspots after text scoring and DFS (\(j_k \ge 0.10\)). Transitions below the threshold keep their text-only q. Report text-only and post-lookup values side by side, with the source for each changed entry.

Any object used as an explanation must be scored itself; its jitter travels along the DFS edge.

## 4. Graph and DFS

- Build a directed graph of objects and the causal/relational links actually supplied.
- DFS (or longest supporting path) measures depth before a change.
- Shallow depth prevents dilution of a supporting node’s jitter.

## 5. Calibration protocol

Status: provisional. Single scorer (Claude), n = 10, conventions fixed before scoring.
The calibration set is the ten biographies below. Worked examples (such as Caesar) are not part of it.

### Sources (fixed before scoring)
Calibration set: one English Wikipedia article per figure, first ~100,000 characters read (the fetch limit; this covers the life narrative, not the later legacy sections):
Srinivasa Ramanujan, Marie Curie, Charles Darwin, Abraham Lincoln, Florence Nightingale, Albert Einstein, Mahatma Gandhi, Ada Lovelace, Frederick Douglass, Jane Austen (all `en.wikipedia.org/wiki/<Name>`; Gandhi = `Mahatma_Gandhi`).
Evidence rule: only what the page says. Where the page gives no cause for a change, q = 0.
Lookup (historical mode): hotspots (j ≥ 0.10) after text scoring are checked against external records per §3; all other entries stay page-only.

Worked-example sources (not used to set the bands):
- *Julius Caesar* (Shakespeare), MIT Shakespeare edition, read in full (shakespeare.mit.edu/julius_caesar). Pure fiction mode: only the play's text may raise a q. Bridge nodes are drawn from the same text.
- *The Lord of the Rings* (Tolkien), whole text. Pure fiction mode.
- *Harry Potter* series (Rowling), books 1, 4, 5 and 7 for the Killing Curse rule. Pure fiction mode.
- Vālmīki *Rāmāyaṇa*, Baroda critical edition (English: Goldman et al., Princeton). Potentially historical mode. Scope: Books 1–6 (Bāla–Yuddha), fixed by philological dating independent of the scores; the Uttara Kāṇḍa is scored separately as a later layer. Later retellings (Purāṇas, Adhyātma Rāmāyaṇa, Kamba, Tulsidas) are outside scope and may not raise q.
- *Mein Kampf*, vol. 1 (autobiographical chapters). Historical mode; hotspot lookup against Kershaw, *Hitler 1889–1936: Hubris* (1998) and Hamann, *Hitler's Vienna* (1999).

Worked examples were scored by a single scorer; contexts carry chapter or book references so entries can be checked against the text.

### Protocol
- Five contexts per life: C1 origin, C2 formative turn, C3 peak work, C4 major adversity, C5 late life and death. Four transitions each.
- A fact sheet per figure was extracted from the page by a summarizing step; the scorer then scored from those sheets.
- A parameter transition is scored only when the sheet reports a change. Parameters used: A00 comfort, A02 motivation, A03 temperament, A05 ending (death only), A06 beliefs, A07 social circle, A08 quirks, A09 purpose, A10 untoward events. A01, A04 and A11 were not extracted.

### Conventions (the skill does not quantify "large")
- Raw Δ: gradual 0.05; abrupt change in intensity or wobble 0.40; orientation flip 0.60; death after a described illness or old age: gradual (0.05); sudden or unheralded: abrupt (0.40); q set by the stated cause.
- q: direct 0.90; one-step 0.75; multi-step or weak 0.50; speculative 0.25; page silent 0.00.
- A10 (untoward events): q reflects whether the event is clearly stated (0.90), not why it happened, as can be checked in the Caesar run below.
- Autobiography sources: a bridge that rests only on the narrator's after-the-fact account of his own inner change (belief, purpose, temperament) is retrospective (S, 0.25). Dated, externally checkable events are coded on directness as usual.

## 6. Calibration on 10 biographies (historical mode)

Values are post-lookup: each figure was scored on its page first, then every hotspot (j ≥ 0.10) was checked against external records per §3. Text-only values are listed below the table.

| Figure | K | J_rms | J_peak | J_mean (legacy, 48 slots) | Hotspots (≥0.10) | Lookup |
|---|---|---|---|---|---|---|
| Ramanujan | 13 | 0.035 | 0.060 | 0.008 | 0 | not needed |
| Curie | 19 | 0.026 | 0.040 | 0.008 | 0 | done (1 entry) |
| Darwin | 19 | 0.031 | 0.060 | 0.010 | 0 | done (1 entry) |
| Lincoln | 18 | 0.029 | 0.050 | 0.008 | 0 | done (2 entries) |
| Nightingale | 11 | **0.077** | 0.200 | 0.013 | 2 | done (2 entries) |
| Einstein | 16 | 0.040 | 0.060 | 0.012 | 0 | done (1 entry) |
| Gandhi | 18 | 0.034 | 0.060 | 0.011 | 0 | not needed |
| Lovelace | 15 | 0.024 | 0.040 | 0.006 | 0 | done (1 entry) |
| Douglass | 14 | 0.042 | 0.100 | 0.010 | 1 | done (2 entries, 1 unchanged) |
| Austen | 16 | 0.034 | 0.050 | 0.010 | 0 | not needed |
| **Mean ± SD** | | **0.037 ± 0.015** | 0.072 ± 0.048 | 0.010 ± 0.002 | 3 total | |

SD is the sample SD (n − 1).

Provisional J_rms bands (mean + 1, 2, 3 SD): ≤ 0.052 smooth; 0.052–0.067 moderate; 0.067–0.082 noticeable gaps; > 0.082 high.

Text-only (page-only) values for comparison:

| Figure | J_rms | J_peak | J_mean | Hotspots |
|---|---|---|---|---|
| Curie | 0.034 | 0.100 | 0.009 | 1 |
| Darwin | 0.038 | 0.100 | 0.012 | 1 |
| Lincoln | 0.042 | 0.100 | 0.011 | 2 |
| Nightingale | 0.155 | 0.400 | 0.022 | 2 |
| Einstein | 0.046 | 0.100 | 0.013 | 1 |
| Lovelace | 0.034 | 0.100 | 0.007 | 1 |
| Douglass | 0.049 | 0.100 | 0.012 | 2 |
| **Set mean ± SD** | **0.050 ± 0.037** | 0.117 ± 0.102 | 0.011 ± 0.004 | 10 total |

Text-only bands were 0.087 / 0.125 / 0.162. Ramanujan, Gandhi and Austen had no hotspots and are unchanged.

### What the data say
1. **Nightingale is the one real outlier, and her residual is genuine.** Text-only, her T3 comfort and temperament steps had a speculative bridge and no bridge (J_rms 0.155). Wikipedia itself mentions the brucellosis attribution, so the fact sheet missed it. The lookup (Crimean fever in May 1855 leading to chronic brucellosis; Young, BMJ 1995) brings her to 0.077. Her remaining hotspots are T3 A03 (0.20) and T3 A00 (0.10), because the cause of her depression is still debated. She sits about 2.7 SD above the set mean.
2. **Lookup removed 7 of 10 hotspots.** Seven of the eight transitions at exactly j = 0.10 got a direct external bridge (0.10 → 0.04). Douglass's sudden death (T4 A05) did not: sources confirm heart failure at 77 but give no prior condition, so the rule leaves the residual in place.
3. **The bands are tied to the conventions.** In the text-only run, moving the raw Δ scale (×0.67 to ×1.5) and q shift (−0.10 to +0.05) moved the set mean from 0.028 to 0.110. This sensitivity check has not been rerun after lookup. Publish the numeric conventions with the bands or the bands will not transfer.
4. **Page-only evidence is not the main driver.** 15 of 159 scored transitions have a "page silent" cause, all gradual steps below the lookup trigger. Giving those a weak bridge (q 0.50) lowers the mean only from 0.037 to 0.035.
5. **The bands rest on one figure.** Without Nightingale the other nine are 0.033 ± 0.006, an SD about the size of one q step on a gradual change. The band spread mostly reflects Nightingale, so treat the bands as provisional until the calibration set grows.

### Limitations
- One scorer, no inter-rater check; the fact sheets themselves involve a summarizing step.
- First ~100,000 characters of each page; legacy sections unread.
- A01, A04, A11 not scored; A05 only at death (9 parameters effective).
- n = 10; Nightingale raises the SD from 0.006 (other nine) to 0.015.
- External lookup covers hotspots only (by design); one lookup per entry, single scorer.
- Wikipedia is a secondary source; its smoothness reflects editors' narrative choices.

### Scored entries (transition, parameter, raw code, q code)
Raw: G gradual 0.05 (also death after a described illness or old age), A abrupt 0.40 (also sudden death), F flip 0.60.
q: D 0.90, O 0.75, M 0.50, S 0.25, N 0.00. Entries are post-lookup.

```
Ramanujan  1:A00 A D, A07 G O, A10 A D, A09 G O | 2:A00 A D, A07 A D, A09 G D, A06 F D | 3:A00 G M, A10 A D | 4:A00 A D, A07 A D, A05 G O
Curie      1:A00 G D, A02 G D, A07 G D, A09 G D | 2:A00 G D, A07 G D, A02 G D, A09 G D | 3:A00 A D, A07 A D, A03 A D, A02 A D, A09 A D, A10 A D | 4:A00 G O, A02 A D, A09 A D, A07 G D, A05 G O
Darwin     1:A00 A D, A02 G D, A07 A D, A09 F D | 2:A00 G O, A02 G D, A06 G M, A09 G D, A07 G O, A10 A D | 3:A00 A D, A02 A D, A07 A D, A10 A D | 4:A00 G O, A07 G O, A09 G D, A06 G N, A05 G O
Lincoln    1:A00 A D, A02 G N, A07 A D, A09 G N | 2:A00 G D, A02 G D, A06 G D, A07 G D, A09 G D | 3:A00 G D, A02 G D, A07 G D, A10 A D | 4:A00 A D, A02 G D, A06 G D, A10 A D, A05 A D
Nightingale 1:A02 G N, A07 G D | 2:A00 A D, A07 A D, A09 A D | 3:A00 A O, A03 A M, A09 G N | 4:A00 G N, A09 G N, A05 G O
Einstein   1:A00 A D, A07 G O, A02 G N | 2:A00 G D, A02 G N, A07 G D | 3:A00 A D, A06 A D, A09 F D, A07 A D, A10 A D | 4:A00 G D, A09 G N, A06 G N, A03 G N, A05 A D
Gandhi     1:A00 A D, A06 G D, A07 A D, A09 A D, A03 G O | 2:A02 G D, A06 G D, A07 G D, A03 G D, A00 G O | 3:A00 A D, A07 A D, A02 F D, A10 A D | 4:A00 A D, A02 A D, A08 G N, A05 A D
Lovelace   1:A02 G D, A07 G D, A00 G O | 2:A00 A D, A09 G D, A10 A D | 3:A00 A D, A07 G O, A02 G O, A08 G S | 4:A00 A D, A07 G D, A06 G D, A09 G D, A05 G O
Douglass   1:A00 A D, A07 A D, A10 A D | 2:A02 G D, A09 G D, A07 G D, A06 G M, A00 A D | 3:A00 A D, A10 A D | 4:A00 A D, A09 A D, A02 A D, A05 A O
Austen     1:A07 G D, A00 A D, A10 A D | 2:A00 A D, A07 A D, A02 G M, A09 G M, A10 A D | 3:A00 A D, A03 G N, A02 G O, A10 A D | 4:A00 G D, A02 G O, A03 G N, A05 G M
```

### Lookup log (text-only → post-lookup)

| Figure | Step | Param | Code | j | External bridge |
|---|---|---|---|---|---|
| Nightingale | T3 | A00 | A S → A O | 0.30 → 0.10 | Crimean fever (May 1855), relapses after 1856, provisionally chronic brucellosis (Young, BMJ 1995) |
| Nightingale | T3 | A03 | A N → A M | 0.40 → 0.20 | Weak chain only: illness, overwork on *Notes*, contested retrospective diagnoses |
| Curie | T3 | A03 | A O → A D | 0.10 → 0.04 | Mourning journal begun days after Pierre's death (1906) records the change directly (AIP Curie exhibit; Eve Curie, 1937) |
| Darwin | T2 | A10 | A O → A D | 0.10 → 0.04 | Illness recorded in his letters from 1837 (Darwin Correspondence Project); A10 q reflects clarity of the event |
| Lincoln | T1 | A00 | A O → A D | 0.10 → 0.04 | 1860 autobiography: move to Indiana "partly on account of slavery, but chiefly… land titles"; mother's death 1818 |
| Lincoln | T1 | A07 | A O → A D | 0.10 → 0.04 | Same source: father's remarriage (1819) and step-siblings joining the household |
| Einstein | T4 | A05 | A O → A D | 0.10 → 0.04 | Aortic aneurysm diagnosed and wrapped (Nissen, 1948); rupture April 1955; refused surgery |
| Lovelace | T4 | A00 | A O → A D | 0.10 → 0.04 | Months-long cancer illness; Lady Byron controlled access (gambling-debt details disputed, not used) |
| Douglass | T3 | A00 | A O → A D | 0.10 → 0.04 | Letter to Brown found after Harpers Ferry; fled to Canada, then England, to avoid arrest (LOC timeline) |
| Douglass | T4 | A05 | A O (unchanged) | 0.10 | Sources confirm sudden heart failure at 77 but give no prior condition; no stronger bridge than the page |

## Worked examples (not part of the calibration)

Same conventions as §5. Raw: G 0.05, A 0.40, F 0.60. q: D 0.90, O 0.75, M 0.50, S 0.25, N 0.00. Bands are biography bands, used for orientation; fiction cannot use external lookup.

| Example | Mode | Focal K | Focal J_rms | J_peak | Focal hotspots | J_mean (legacy) | J_plot (K_total) |
|---|---|---|---|---|---|---|---|
| Caesar | Pure fiction | 20 | 0.089 | 0.200 | 8 | 0.031 | 0.106 (27) |
| Frodo | Pure fiction | 22 | 0.048 | 0.150 | 1 | 0.017 | 0.118 (39) |
| Avada Kedavra rule | Pure fiction | 8 | 0.198 | 0.400 | 5 | 0.059 (20 slots) | 0.182 (11) |
| Rama (Books 1–6) | Potentially historical | 13 | 0.033 | 0.040 | 0 | 0.011 (36 slots) | — |
| *Mein Kampf* narrator | Historical (autobiography) | 18 | 0.183 (text-only 0.186) | 0.450 | 4 (text-only 6) | 0.041 (text-only 0.044) | — |

### Worked example: plot-level jitter for *Julius Caesar* (pure fiction)

**Node set.** Start at Caesar (living, 12 parameters), then add every node used to raise a q, to depth 1:
Calpurnia (living), the omens, Cassius's report ("superstitious grown of late"), and the conspiracy.
Decius Brutus is listed but has K = 0, so he is not counted.

**Contexts (4 transitions).** T1 I.ii → II.ii (yields to Calpurnia); T2 → goes after Decius;
T3 → III.i (assassination); T4 → ghost (IV.iii / V).

**Per-node results**

| Node | K | J_rms | J_peak | Hotspots | Share of Σj² | Evidence |
|---|---|---|---|---|---|---|
| Caesar | 20 | 0.089 | 0.200 | 8 | 52.0% | ok |
| Calpurnia | 3 | 0.040 | 0.040 | 0 | 1.6% | low (K < 8) |
| Omens | 2 | 0.071 | 0.100 | 1 | 3.3% | low |
| Cassius's report | 1 | 0.300 | 0.300 | 1 | 29.8% | low |
| Conspiracy | 1 | 0.200 | 0.200 | 1 | 13.2% | low |

Caesar's legacy J_mean (48 slots) = 0.031.

**Plot-level result.** J_plot = √( Σ j² / K_total ) = √(0.302 / 27) = **0.106** (K_total = 27).
For comparison, the unweighted RMS of the five node J_rms values is 0.170. That figure is inflated because two one-transition
nodes count as much as Caesar's 20. This is why the pooled form is the one to report.

**Combined hotspots (j ≥ 0.10)**

| j | Node | Step | Parameter | raw Δ | q | Why |
|---|---|---|---|---|---|---|
| 0.30 | Cassius's report | T1 | A03 | 0.40 | 0.25 | Biased, retrospective speaker |
| 0.20 | Conspiracy | T1 | A03 | 0.40 | 0.50 | "Sacrificers, not butchers" becomes "bathe hands in blood" |
| 0.20 | Caesar | T1 | A06 | 0.40 | 0.50 | Waves off the soothsayer, then consults the priests (multi-step) |
| 0.15 | Caesar | T4 | A03 | 0.60 | 0.75 | The ghost is predicted but not explained |
| 0.15 | Caesar | T4 | A05 | 0.60 | 0.75 | Continuation as a ghost, predicted but not explained |
| 0.10 | Caesar | T1 | A03 | 0.40 | 0.75 | Augurers find no heart in the beast, then Calpurnia's plea; one-step |
| 0.10 | Caesar | T4 | A04, A07, A08, A11 | 0.40 | 0.75 | Same ghost step |
| 0.10 | Omens | T1 | A03 | 0.40 | 0.75 | Casca and Cicero disagree on the meaning |

**Reading.** J_plot is 0.106. For orientation only, it sits above the top biography band edge (0.082), and Caesar's own J_rms (0.089) does too. The bands come from historical-mode scores after hotspot lookup, while Caesar is scored in pure fiction mode, where no lookup is allowed, so this comparison overstates the gap. The two worst values sit at the bridges
(Cassius's retrospective claim and the conspiracy's shift in tone). Caesar's own weak points are the belief shift at T1 and the
ghost step at T4.

**Caveats.**
- Bridge-node entries were scored for reproducibility, and all four have K < 8. Treat them as low evidence, not as a verdict.
- Antony's prophecy (III.i, "Caesar's spirit, ranging for revenge") is the bridge for the T4 ghost transitions,
  and Decius's flattery supports the T2 values. Neither is scored as a node, which the "explanation is not free" rule
  requires. J_plot is therefore probably slightly underestimated.
- Gaps between nodes (for example, what links the conspiracy to the omens) are not visible to J_plot and are flagged by hand.

```
Caesar     1:A00 A D, A02 F D, A03 A O, A06 A M, A09 G O, A10 A D | 2:A00 A D, A02 F D, A03 A D, A06 A D | 3:A03 G D, A05 A D, A07 A D | 4:A03 F O, A04 A O, A05 F O, A07 A O, A08 A O, A09 F D, A11 A O
Calpurnia  1:A00 A D, A02 A D, A06 A D
Omens      1:A03 A O | 2:A00 G D
Cassius    1:A03 A S
Conspiracy 1:A03 A M
```

### Worked example: Frodo, *The Lord of the Rings* (pure fiction)

**Contexts.** C0 Origin (parents killed in a boating accident at 12; Brandy Hall; adopted by Bilbo at 21) → C1 Bag End, Gandalf's revelation → C2 Council of Elrond → C3 Breaking of the Fellowship → C4 Mount Doom → C5 Grey Havens. Transitions from C0 are numbered 0.

**Node set (bridge-only, materiality rule).** Frodo (9 parameters), the Ring, Gollum, Gandalf (T1), Isildur (claim step), Bilbo (adoption), Boromir (T2), Faramir (T3). Listed with K = 0: the Brandybucks.

| Node | K | J_rms | J_peak | Hotspots | Share of Σj² | Evidence |
|---|---|---|---|---|---|---|
| Frodo | 22 | 0.048 | 0.150 | 1 | 9.5% | ok |
| The Ring | 2 | 0.029 | 0.040 | 0 | 0.3% | low (K < 8) |
| Gollum | 4 | 0.051 | 0.060 | 0 | 1.9% | low |
| Gandalf | 4 | 0.048 | 0.060 | 0 | 1.7% | low |
| Isildur | 2 | 0.127 | 0.150 | 2 | 6.0% | low |
| Bilbo | 1 | 0.400 | 0.400 | 1 | 29.7% | low |
| Boromir | 3 | 0.290 | 0.400 | 2 | 46.7% | low |
| Faramir | 1 | 0.150 | 0.150 | 1 | 4.2% | low |

J_plot = √(0.539 / 39) = **0.118** (K_total = 39). Low-evidence nodes contribute 90% of Σj². Frodo's coverage = 22 / 36 = 0.61; legacy J_mean (48 slots) = 0.017.
Sensitivity: silent pages at q 0.50 give 0.086; coding Boromir's repentance and Bilbo's adoption as gradual gives 0.076; dropping Bilbo, Boromir or Faramir gives 0.100, 0.089 and 0.117; the previous node set gives 0.056.

| j | Node | Step | Param | raw Δ | q | Why |
|---|---|---|---|---|---|---|
| 0.40 | Boromir | T2 | A03 | 0.40 | 0.00 | Repents at once after seizing for the Ring; the page does not explain why at that moment |
| 0.40 | Bilbo | T0 | A07 | 0.40 | 0.00 | Adopts Frodo at 21; neither page gives a cause |
| 0.30 | Boromir | T2 | A06 | 0.60 | 0.50 | Accepts the Council's ruling, then urges using the Ring; page cites desperation and belief in his integrity, not what changed |
| 0.15 | Isildur | T1 | A02 | 0.60 | 0.75 | Keeps the Ring; only cause is the Ring's influence |
| 0.15 | Frodo | T3 | A02 | 0.60 | 0.75 | Claims the Ring at the chasm; only cause is the Ring's pull |
| 0.15 | Faramir | T3 | A02 | 0.60 | 0.75 | Releases and protects the hobbits; no explicit reason beyond wisdom about the Ring |
| 0.10 | Isildur | T1 | A03 | 0.40 | 0.75 | Rejects Elrond's and Círdan's counsel |

**Reading.** Frodo's own score is smooth (0.048). J_plot is high by orientation (0.118), but 76% of it comes from two entries (Bilbo's adoption and Boromir's repentance), both coded abrupt with no cause on the pages.

```
Frodo   0:A00 A D, A07 A D, A10 A D | 1:A00 A D, A02 F D, A07 G N, A10 A D, A09 G D | 2:A10 A D, A07 A D, A02 G N, A03 G D | 3:A03 G D, A02 F O, A07 A D, A10 A D | 4:A00 A D, A10 A D, A05 G D, A07 G N, A09 G D, A03 G D
Bilbo   0:A07 A N
Boromir 2:A06 F M, A03 A N, A05 A D
Faramir 3:A02 F O
```

### Worked example: the Killing Curse rule, *Harry Potter* (pure fiction, known-loophole test)

**Node set (depth 1).** The Avada Kedavra rule (non-living, A00–A04), Lily's protection (non-living), the Elder Wand (non-living).

**Contexts.** C1 *Philosopher's Stone* (Harry's survival; Dumbledore explains Lily's sacrifice) → C2 *Goblet of Fire* (rule stated: no countercurse, no blocking; wands connect in the graveyard) → C3 *Order of the Phoenix* (objects and Fawkes intercept the curse in the Ministry atrium) → C4 *Deathly Hallows*, the Forest (Harry struck and returns; King's Cross) → C5 *Deathly Hallows*, final duel (curse rebounds).

| Node | K | J_rms | J_peak | Hotspots | Share of Σj² | Evidence |
|---|---|---|---|---|---|---|
| Avada Kedavra rule | 8 | 0.198 | 0.400 | 5 | 86.2% | ok (K = 8) |
| Lily's protection | 2 | 0.141 | 0.200 | 1 | 11.0% | low (K < 8) |
| Elder Wand | 1 | 0.100 | 0.100 | 1 | 2.8% | low |

J_plot = √(0.363 / 11) = **0.182** (K_total = 11). Rule's legacy J_mean (20 slots, non-living) = 0.059.

| j | Node | Step | Parameter | raw Δ | q | Why |
|---|---|---|---|---|---|---|
| 0.40 | AK rule | T2 | A03 | 0.40 | 0.00 | Stated as unblockable, then intercepted by objects and a phoenix; no in-text reconciliation |
| 0.30 | AK rule | T3 | A03 | 0.60 | 0.50 | Harry struck and survives; blood-tether and Horcrux explanation given only at King's Cross (multi-step) |
| 0.20 | AK rule | T3 | A00 | 0.40 | 0.50 | The curse hits but kills only the fragment; same multi-step bridge |
| 0.20 | Lily's protection | T3 | A04 | 0.40 | 0.50 | Protection extended through Voldemort's blood, revealed retrospectively |
| 0.10 | AK rule | T4 | A03 | 0.40 | 0.75 | Rebound explained by Elder Wand allegiance, a rule introduced only in book 7 |
| 0.10 | AK rule | T4 | A04 | 0.40 | 0.75 | Harry's sacrifice protects the defenders, by analogy to Lily's |
| 0.10 | Elder Wand | T4 | A00 | 0.40 | 0.75 | Allegiance chain spelled out in the duel; transfer-by-disarming rule introduced late |

**Reading.** Highest J_rms of all runs. The loophole is T2: an absolute rule broken with no prior mechanism. If "blocking" is read as "magical shielding only" (q S instead of N), J_rms is 0.174, still high.

**Caveats.** The Horcrux in Harry (bridge for T3) is not scored as a node. K = 8 is at the evidence minimum.

```
AK_rule      1:A03 A D, A04 A D | 2:A03 A N | 3:A03 F M, A00 A M | 4:A03 A O, A04 A O, A00 G D
Lily_prot    1:A02 G D | 3:A04 A M
Elder_Wand   4:A00 A O
```

### Worked example: Rama, Vālmīki *Rāmāyaṇa* (potentially historical)

**Scope.** Books 1–6 (Bāla to Yuddha). The Uttara Kāṇḍa is widely regarded as a later addition and is scored separately; it does not enter Rama's score. Scope was set by philological dating, not by the jitter values. The Bāla Kāṇḍa is also often dated later; its transitions are all low, and dropping it changes J_rms only to about 0.037 (K = 8).

**Contexts.** C1 Bāla (education, marriage to Sītā) → C2 Ayodhyā (exile through Kaikeyī's boons) → C3 Araṇya–Kiṣkindhā (abduction; alliance with Sugrīva; Vāli killed) → C4 Yuddha (war; rejection and fire ordeal of Sītā). Separate: C4 → C5 Uttara (Sītā abandoned; Śambūka; entry into the Sarayu).

| Object | K | J_rms | J_peak | Hotspots | J_mean (legacy) |
|---|---|---|---|---|---|
| Rama, Books 1–6 | 13 | 0.033 | 0.040 | 0 | 0.011 (36 slots) |
| Uttara step, reported separately | 5 | 0.128 | 0.200 | 2 | — |

| j | Step | Parameter | raw Δ | q | Why |
|---|---|---|---|---|---|
| 0.04 | T2 | A03 | 0.40 | 0.90 | Kills Vāli from concealment. Kiṣkindhā 18 gives a direct justification (Vāli's taking of Rumā; Rama acting under Bharata's sovereignty; the hunting argument), and Vāli accepts it, asks forgiveness and entrusts Aṅgada to Rama |
| 0.04 | T3 | A03 | 0.60 | 0.90 | Public rejection of Sītā. After the fire ordeal Rama states he knew she was pure and acted for the world's conviction, so his orientation did not reverse: abrupt change in conduct, not a flip |

Not used as bridges (outside the Vālmīki scope): Vāli's half-strength boon and the Jara–Krishna karmic link (later retellings and Purāṇic tradition); Chāyā Sītā (Kūrma and Brahmavaivarta Purāṇas, Adhyātma Rāmāyaṇa, Tulsidas). Rama's revealed divinity was not used as a bridge.

**Uttara step (separate).** Abandonment of Sītā over public gossip (A00, q 0.50) and the Śambūka episode (A03, q 0.50) give j = 0.20 each. High jitter here is consistent with its dating as a later layer.

**Sensitivity.** Vāli q / Sītā q = 0.90 / 0.90 → 0.040; 0.75 / 0.75 → 0.049 (both smooth); 0.50 / 0.90 → 0.064 (moderate).

**Reading.** Smooth, at the level of the biographies. The two turns that read as abrupt both have direct in-text bridges.

**Caveats.** Some scholars treat the fire-ordeal and divine-revelation passages as expansions within the Yuddha Kāṇḍa; if excluded, the T3 bridge weakens to one-step (J_rms 0.042, still smooth). Kaikeyī's boons and Śūrpaṇakhā are bridges not scored as nodes. A06 not scored.

```
Rama    1:A00 A D, A02 G D, A07 A D, A09 G D, A03 G D | 2:A00 A D, A10 A D, A07 A D, A03 A D | 3:A02 G D, A10 A D, A07 A D, A03 A D
Uttara  4:A00 A M, A03 A M, A05 G D, A07 A D, A09 G D
```

### Worked example: the narrator of *Mein Kampf*, vol. 1 (historical, autobiography)

Scored as a historical source text. The score measures how the narrative bridges its own changes, not its content.

**Contexts.** C1 Linz (conflict with father over career) → C2 Vienna 1908–1913 (Academy rejection, poverty, claimed conversion) → C3 Munich and the war 1913–1918 → C4 Pasewalk, November 1918 (claimed decision to enter politics) → C5 1919–1920 (joins the DAP, becomes a speaker).

| Object | K | J_rms | J_peak | Hotspots | J_mean (legacy) |
|---|---|---|---|---|---|
| Narrator, text-only | 18 | 0.186 | 0.450 | 6 | 0.044 |
| Narrator, post-lookup | 18 | **0.183** | 0.450 | 4 | 0.041 |

| Step | Param | Text-only | Post-lookup | Lookup finding |
|---|---|---|---|---|
| T1 | A06 belief | F S (0.45) | unchanged | Hamann documents Jewish acquaintances and dealers in Vienna; no contemporaneous evidence of the claimed conversion (contradiction) |
| T1 | A09 purpose | A S (0.30) | unchanged | No evidence of a political programme in Vienna (silence) |
| T2 | A00 comfort | A O (0.10) | **A D (0.04)** | Move to Munich (1913) bridged directly by external record (avoiding Austrian military service); text omits this motive |
| T3 | A09 purpose | F S (0.45) | unchanged | Kershaw: politicisation came through army propaganda work in 1919, not a Pasewalk resolve (contradiction) |
| T3 | A03 temperament | A S (0.30) | unchanged | Comrades' accounts describe a loner; no evidence of agitation before 1919 (silence) |
| T4 | A11 oratory | A O (0.10) | **A D (0.04)** | 1919 course reports noted his speaking (corroboration) |

**Reading.** The dated events hold up. The narrator's account of his own turning points is either contradicted or unsupported by the record. That is what retrospective smoothing looks like here: continuous prose whose bridges are supplied by the narrator alone. The T2 omission (the motive for the move) is flagged by hand, since J_rms cannot see a discrepancy once the change is bridged.

**Caveats.** Scored under the autobiography convention (§5), so it is not directly comparable to the Wikipedia-scored calibration set.

```
Narrator_text  1:A00 A D, A02 A D, A10 A D, A06 F S, A09 A S, A07 A D | 2:A00 A O, A02 A D, A07 A D, A10 A D | 3:A10 A D, A00 A D, A09 F S, A03 A S | 4:A07 A D, A11 A O, A02 G D, A09 G D
Narrator_post  1:A00 A D, A02 A D, A10 A D, A06 F S, A09 A S, A07 A D | 2:A00 A D, A02 A D, A07 A D, A10 A D | 3:A10 A D, A00 A D, A09 F S, A03 A S | 4:A07 A D, A11 A D, A02 G D, A09 G D
```

## 7. Test-plan passages for further checks

1. Additional biography excerpts to enlarge the calibration set.
2. Animal Farm objects need to be rescored.
3. Known loophole scenes (absolute rule later violated with no prior mechanism). One done (Killing Curse rule); more are needed for a fiction calibration set.
4. Clean non-living object with single stable use.
5. Second scorer for the calibration set and the worked examples (inter-rater check).

## 8. Reporting template

- Per object: living?, parameters used, K, coverage, J_rms, J_peak, J_mean (legacy), hotspots, supporting nodes (reported separately)
- Per story: node set and depth, J_plot (pooled), K_total, worst node J_rms, combined hotspots, inter-node gaps, number of K<8 nodes
- Mode (fiction or historical) and whether external sources altered any q
- Conventions used; comparison to calibration mean 0.037 and bands 0.052 / 0.067 / 0.082; flag "low evidence" if K < 8
