# System Degradation Framework — Scientific Discoveries

**Patent #4 — Adaptive Threshold System for Degradation Detection in Mechanical and Electrochemical Systems**

**Application Number:** 63/921,348 (filed November 20, 2025)

**Repository:** https://github.com/Wise314/system-degradation-framework

**Paper:** [Zenodo DOI 10.5281/zenodo.20052865](https://doi.org/10.5281/zenodo.20052865)

---

## Discovery 1: Identity Collapse is a Measurable and Distinct Degradation Regime

**Finding:** Degrading physical systems split into two regimes: identity-preserving degradation (smooth capacity loss, behavioral character maintained) and identity-collapse degradation (substantial loss of baseline signal identity). These regimes are empirically separable using I = baseline_signal / current_signal.

**Evidence:** Battery B0005 end-state I = 0.714, rho = 0.998 (identity preserved, smooth degradation). Bearing1_1 end-state I = 0.108, rho = 0.978 (identity collapsed, temporal smoothness preserved).

---

## Discovery 2: Identity and Temporal Coherence Carry Distinct Information

**Finding:** Identity and temporal coherence are empirically distinct system properties. A system can exhibit severe identity collapse while maintaining high temporal coherence, so the two quantities should not be conflated.

**Evidence:** Bearing1_1: I = 0.108, rho = 0.978. Battery B0005: I = 0.714, rho = 0.998. Both systems have high rho; only the bearing has low I.

---

## Discovery 3: Linear Identity Modulation Over-Corrects in Extreme Collapse, Square Root Dampening is the Successful Correction

**Finding:** Linear I × rho as a threshold multiplier drives excessive threshold reduction in extreme-collapse systems (94% reduction at I = 0.061), causing detection failure. Square root identity dampening reduces this to 76%, restoring detection performance.

**Evidence:** Linear multiplier on Bearing2_4: threshold reduction 94%, F1 = 0.455 (FAIL). Square root multiplier: threshold reduction 76%, F1 = 0.957 (PASS). Same data, same alpha, same bearing.

---

## Discovery 4: Empirical Rescue Boundary Near I ≈ 0.25 in the Tested Bearing Set

**Finding:** In the tested XJTU-SY bearing set, an empirical rescue boundary appears near I ≈ 0.25. Config 2 rescues all 10 validated passing bearings below this range. The validator documentation reports no successful rescue among the tested failing bearings at or above this regime or in no-signal cases.

**Evidence:** All 10 passing bearings have I below approximately 0.25 (range 0.044 to 0.248). Failing cases include no-signal bearings and bearings outside the effective rescue regime in the tested set.

---

## Discovery 5: Domain-Specific Alpha is Required and Cannot Be Assumed

**Finding:** The coupling constant alpha must be tuned per physical domain. Bearings use alpha = 0.1 and batteries use alpha = 0.034. Applying the wrong alpha actively harms detection performance.

**Evidence:** Batteries with alpha = 1.0 produced F1 drops of 37 to 43 percentage points. Correcting to alpha = 0.034 restored positive results across all three validated batteries. Bearing alpha = 0.1 validated across all 10 passing bearings.

---

**Last Updated:** May 6, 2026
