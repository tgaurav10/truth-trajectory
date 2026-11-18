# Changelog

## 2026-11-18
- Added post-lookup calibration adjustments for the 10 biographies.
- Updated scored entries and lookup notes for several figures (including Lincoln, Gandhi, Nightingale).
- Incorporated additional biography and fiction test material.

## 2026-11-15
- Added plot-level jitter (J_plot) that pools transitions across the graph without averaging node scores into each other.
- Clarified supporting-node reporting and low-evidence rules at both object and plot level.

## 2026-11-14
- Switched headline metric from mean residual to J_rms (root-mean-square of transition residuals).
- Restricted scoring to effective parameters only (unmoved or unmentioned parameters produce no transition).
- Added J_peak, hotspots, K/coverage, and the K < 8 low-evidence flag.
- Retained J_mean as a legacy figure for comparability with the original calibration.
- Formalised raw Δ as mean step size on the active VETCESC variable(s) (t, r, θ, φ).
- Formalised q via the causal-inference scale (direct / one-step / multi-step / speculative / none) with optional decay function.
- Updated calibration table and interpretation bands to the new J_rms values (mean ≈ 0.037 post-lookup).
- Initial public release as Truth Trajectory.
- Parameters derived from the VETCESC framework.