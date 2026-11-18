# truth-trajectory
Measures residual jitter on narrative trajectories to distinguish smooth history from fiction, loopholes, and modified records.

An open-source method and LLM skill that scores stories, myths, biographies, and historical claims by how continuously key parameters (origin, purpose, temperament, belief, social bonds, etc.) evolve. Low residual jitter = well-bridged trajectory. High residual jitter = unexplained jumps or later insertions.

Calibrated on 10 well-documented biographies (mean J_rms ≈ 0.037, provisional).

## Core idea

Narratives are treated as trajectories through a fixed parameter space.  
Jitter is the unexplained change in a parameter after a small context shift:

jitter = raw_Δ × (1 - q)

- `raw_Δ` = absolute change in the parameter’s 0–1 state
- `q` = explanation quality (0 = no bridge, 1 = fully accounted for)

Pure fiction is scored on the text alone. Historical or potentially historical accounts are scored on the text first; external archives are consulted only for hotspots (transitions with jitter ≥ 0.10). Supporting objects are walked with DFS so explanatory burden is transferred, not hidden.

## Results from all runs

Headline metric is J_rms (root mean square of residual jitter over scored transitions). Bands come from the biography calibration; fiction and plot-level (J_plot) scores are mapped to those bands for orientation.

| Example | Type | Mode | K | J_rms | J_peak | Hotspots | J_plot | Band (J_rms) |
|---|---|---|---|---|---|---|---|---|
| Lovelace | Biography | Historical (calibration) | 15 | 0.024 | 0.040 | 0 | — | smooth |
| Curie | Biography | Historical (calibration) | 19 | 0.026 | 0.040 | 0 | — | smooth |
| Lincoln | Biography | Historical (calibration) | 18 | 0.029 | 0.050 | 0 | — | smooth |
| Darwin | Biography | Historical (calibration) | 19 | 0.031 | 0.060 | 0 | — | smooth |
| Gandhi | Biography | Historical (calibration) | 18 | 0.034 | 0.060 | 0 | — | smooth |
| Austen | Biography | Historical (calibration) | 16 | 0.034 | 0.050 | 0 | — | smooth |
| Ramanujan | Biography | Historical (calibration) | 13 | 0.035 | 0.060 | 0 | — | smooth |
| Einstein | Biography | Historical (calibration) | 16 | 0.040 | 0.060 | 0 | — | smooth |
| Douglass | Biography | Historical (calibration) | 14 | 0.042 | 0.100 | 1 | — | smooth |
| Nightingale | Biography | Historical (calibration) | 11 | 0.077 | 0.200 | 2 | — | noticeable gaps |
| Rama (Vālmīki, Books 1–6) | Epic hero | Potentially historical | 13 | 0.033 | 0.040 | 0 | — | smooth |
| Frodo (*LOTR*, from Wikipedia pages) | Character | Pure fiction | 22 | 0.048 | 0.150 | 1 | 0.118 | high |
| Caesar (*Julius Caesar*) | Character | Pure fiction | 20 | 0.089 | 0.200 | 8 | 0.106 | high |
| *Mein Kampf* narrator (vol. 1) | Autobiography | Historical | 18 | 0.183 | 0.450 | 4 | — | high |
| Killing Curse rule (*Harry Potter*) | Rule (non-living) | Pure fiction | 8 | 0.198 | 0.400 | 5 | 0.182 | high |

Reading:
- **Biographies** cluster at 0.024–0.042. Nightingale's remaining gap is real: the cause of her post-1857 depression is still debated.
- **Frodo scores smooth.** Well-bridged fictitious character may score smooth, but check the plot score for consistency.
- ***Mein Kampf***: external records confirm the dated events but contradict or fail to support the narrator's account of his own turning points (text-only 0.186, after lookup 0.183).
- **Killing Curse rule**: an absolute rule broken with no in-text mechanism is the clearest loophole in the set.

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
