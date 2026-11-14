# truth-trajectory
Measures residual jitter on narrative trajectories to distinguish smooth history from fiction, loopholes, and modified records.

An open-source method and LLM skill that scores stories, myths, biographies, and historical claims by how continuously key parameters (origin, purpose, temperament, belief, social bonds, etc.) evolve. Low residual jitter = well-bridged trajectory. High residual jitter = unexplained jumps or later insertions.

Calibrated on 10 well-documented biographies (mean residual jitter ≈ 0.05).

## Core idea

Narratives are treated as trajectories through a fixed parameter space.  
Jitter is the unexplained change in a parameter after a small context shift:

jitter = raw_Δ × (1 - q)

- `raw_Δ` = absolute change in the parameter’s 0–1 state
- `q` = explanation quality (0 = no bridge, 1 = fully accounted for)

Pure fiction is scored on the text alone. Historical or potentially historical accounts may import external archives. Supporting objects are walked with DFS so explanatory burden is transferred, not hidden.

## Quick results from tests

| Example | Mode | Residual jitter | Reading |
|---------|------|-----------------|---------|
| Ramanujan (biography) | Historical | ~0.03 | Very smooth |
| Frodo (LOTR) | Fiction | ~0.08 | Smooth, well-bridged |
| Rama (Vālmīki) | Potentially historical | ~0.11 | Internally continuous, limited independent external depth |
| Mein Kampf (autobiography) | Historical | ~0.29 | Noticeable retrospective smoothing |
| Avada Kedavra rule (Harry Potter) | Fiction | ~0.64 | High jitter / unclosed loophole |

## Calibration bands

From 10 biographies (Ramanujan, Curie, Darwin, Lincoln, Nightingale, Einstein, Gandhi, Lovelace, Douglass, Austen):

- ≤ 0.08 — biography-like / very smooth
- 0.08–0.15 — moderate, still largely continuous
- 0.15–0.30 — noticeable gaps or retrospective smoothing
- greater than 0.30 — high residual jitter

## Usage

1. Download or copy the skill files.
2. Place `SKILL.md` (and the `references/` folder) where your LLM or agent expects custom skills.
3. Ask it to score a story, character, or claim. It will classify mode, extract parameters, compute residual jitter, and walk supporting nodes.

See `SKILL.md` for the full workflow and `references/algorithm_and_test_plan.md` for the detailed algorithm, formula, and calibration data.

## References

The trajectory parameters used in this skill are drawn from the VETCESC framework (Vedic Theory of Consciousness Emanating from Super Consciousness), which maps Ahaṁkāra(ego) curvature components to narrative and physical attributes:

- Gaurav, T. (2026). *VETCESC: Vedic Theory of Consciousness Emanating from Super Consciousness*. Zenodo. https://zenodo.org/records/18593395

## License

MIT
