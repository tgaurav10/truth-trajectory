---
name: narrative-plausibility-scorer
description: "Scores any story, myth, or historical account by measuring residual jitter on VETCESC/EFE-derived trajectory parameters. Use when distinguishing fiction from history, validating mythologies, spotting plot holes or modified records, or helping writers close loopholes."
type: workflow
lifecycle: active
---

# Narrative Trajectory Jitter Scorer

Evaluate narratives by measuring how smoothly key objects and characters move through a fixed set of parameters derived from the VETCESC Ahaṁkāra curvature components. High residual jitter signals loopholes, under-developed bridges, or later insertions. Output per-object jitter scores in [0,1] plus an overall trajectory assessment.

## When to Use

- User asks if a text/story/myth is historical or fictional.
- User wants loopholes or unexplained jumps identified.
- User provides a claim, biography, or epic for realism check.
- Writer requests help making a story “sound more real” by closing trajectory gaps.

Do **not** use for pure creative generation or literary criticism unrelated to continuity.

## Core Parameters

Living objects/characters: up to all 12 parameters.  
Non-living objects: only the first 5.

| Param | Narrative meaning |
|-------|-------------------|
| \(A_{00}\) | Comfort / stability of the relationship or use |
| \(A_{01}\) | Origin / how it entered or came into existence |
| \(A_{02}\) | Motivation / intent for using or relating to it |
| \(A_{03}\) | Thinking style, temperament, aggressiveness, passiveness, rigidity or versatility (of objects)|
| \(A_{04}\) | Variety of use-cases or ways the relationship is expressed |
| \(A_{05}\) | Offspring / continuation, or manner of death / ending |
| \(A_{06}\) | Belief system |
| \(A_{07}\) | Social circle size and how the character gels with them |
| \(A_{08}\) | Quirks or imperfections |
| \(A_{09}\) | Stable vision or purpose |
| \(A_{10}\) | Untoward / unpredictable events that hit or merely brushed past (optional) |
| \(A_{11}\) | Unusually strong / rare higher attributes (optional) |

## Core Workflow

1. **Classify the input**
   - Mode: pure fiction / historical fiction / claimed history / myth / biography / potentially historical.
   - Extract major objects and characters. Tag each living or non-living.

2. **Build the graph**
   - Nodes = objects/characters.
   - Directed edges = causal or relational links that the text (or allowed external sources) actually supplies.
   - Use DFS to measure supporting depth before any parameter is allowed to change.

3. **Extract state sequences**
   - For each object and each relevant parameter, sample the state intensity (0–1) at successive context points (scene boundaries, major events, chapter turns).

4. **Compute residual jitter** (see formula below)
   - Apply the two-mode rule when filling gaps.

5. **Report**
   - Per-object residual jitter (mean across its parameters).
   - Highlight any node whose jitter remains high after DFS and (if allowed) external lookup.
   - Overall assessment: low mean jitter + deep supporting chains = smoother / more plausible trajectory.

## Residual Jitter Formula

For each consecutive pair of contexts:

\[
\text{jitter} = \text{raw }\Delta \times (1 - q)
\]

- raw \(\Delta\) = absolute difference in the parameter’s 0–1 state intensity.
- \(q\) = explanation quality [0,1]:
  - 1 = intervening events fully and explicitly account for the change,
  - 0 = no bridging material,
  - intermediate = partial or implicit bridge.

Final parameter jitter = mean of these residual values across the object’s context changes.  
Object jitter = mean of its parameter jitters.  
(Silent parameters that never contradict = 0.)

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

## Two-Mode Rule

| Mode | Allowed sources for raising \(q\) | Effect |
|------|-----------------------------------|--------|
| Pure fiction | Only the text itself | Under-developed bridges keep high residual jitter. DFS exposes them. |
| Historical or potentially historical | Text + external archives, records, biographies, consistent secondary accounts | If external material supplies continuous trajectory, raise \(q\) and lower residual jitter. Silence or contradiction leaves jitter high. |

Explanation is not free: any bridging object or event used to raise \(q\) must itself be scored. Its residual jitter is attached via the DFS edge.

## Interpretation (calibrated on 10 biographies)

Empirical anchor from 10 well-documented lives (Ramanujan, Curie, Darwin, Lincoln, Nightingale, Einstein, Gandhi, Lovelace, Douglass, Austen):  
mean residual jitter ≈ 0.050 (range 0.034–0.07).

| Mean residual jitter | Reading |
|----------------------|---------|
| ≤ 0.08 | Biography-like / very smooth |
| 0.08–0.15 | Moderate, still largely continuous |
| 0.15–0.30 | Noticeable gaps or retrospective smoothing |
| > 0.30 | High residual jitter |

## Resources

- Full algorithm, worked examples, and test-plan passages: `references/algorithm_and_test_plan.md`

Always load the algorithm reference when performing a full evaluation.
