# System Degradation Framework — Scientific Discoveries

**Patent #4 — Adaptive Threshold System for Degradation Detection in Mechanical and Electrochemical Systems**

**Application Number:** 63/921,348 (filed November 20, 2025)

**Repository:** https://github.com/Wise314/system-degradation-framework

**Paper:** [Zenodo DOI 10.5281/zenodo.20052865](https://doi.org/10.5281/zenodo.20052865)

---

## Discovery 1: Identity Collapse is a Measurable and Distinct Degradation Regime

**Finding:** Physical systems do not all fail the same way. They split into two regimes that can be measured and separated. Some systems lose capacity smoothly while keeping their core signal character intact (identity-preserving). Others lose most of their baseline signal character before failing (identity-collapse). The split is measurable using a single ratio: I = baseline signal / current signal.

**Problem it solves:** Before this finding, degradation detection methods treated all failing systems as if they were the same kind of failure. There was no way to distinguish a battery slowly losing capacity from a bearing whose vibration pattern has transformed into something completely different from its baseline.

**Why it matters:** A single threshold formula cannot serve both regimes well. Apply the wrong correction to the wrong regime and detection fails. Knowing which regime a system is in tells you which kind of correction will actually work.

**Methodology:** Diagnostic scripts computed identity on Battery B0005 from the NASA dataset (capacity measurements) and Bearing1_1 from the XJTU-SY dataset (vibration RMS measurements). Identity is defined as baseline signal divided by current signal for bearings (inverted RMS ratio) and current capacity divided by initial capacity for batteries.

**Evidence:** Battery B0005 ends its monitored life with identity I = 0.714 and temporal coherence rho = 0.998 (high identity preserved, smooth degradation). Bearing1_1 ends its life with identity I = 0.108 and temporal coherence rho = 0.978 (identity has collapsed to 11% of baseline, but temporal smoothness is preserved). The two systems are clearly in different regimes by this measurement.

**Negative results:** The moderate-identity regime (between I = 0.25 and I = 0.5) is not yet characterized. None of the validated bearing cases fall in this range. The repo leaves this gap explicitly open rather than claiming it is understood.

**Prior art position:** Existing adaptive threshold methods do not use behavioral identity state as a first-class signal in the threshold formula. This patent introduces it as a primary modulator.

---

## Discovery 2: Identity and Temporal Coherence Carry Distinct Information

**Finding:** Identity (how far the signal has drifted from baseline) and temporal coherence (how smoothly the signal is evolving) are not the same thing. A system can have severe identity collapse and still have very high temporal coherence at the same time. The two properties must be tracked as separate quantities.

**Problem it solves:** A formula that combines these two into one composite number would mask the information needed to choose the right correction. The identity-collapse signal would be hidden behind the smooth coherence signal.

**Why it matters:** Bearing1_1 is the clearest example. Its identity has collapsed to 0.108, but its coherence is 0.978 — almost perfectly smooth. If you collapsed I and rho into one combined number, this bearing would look fine. But it is in failure mode. Keeping the two terms separate is what allows the framework to detect the bearing failure pattern.

**Methodology:** Identity I and temporal coherence rho were computed independently on Bearing1_1 from XJTU-SY data and Battery B0005 from NASA data. For bearings, I = baseline RMS / final RMS. For batteries, I = current capacity / initial capacity. Temporal coherence rho is the Pearson autocorrelation of the signal at lag 1.

**Evidence:** Bearing1_1: I = 0.108, rho = 0.978 (very low identity, very high coherence). Battery B0005: I = 0.714, rho = 0.998 (high identity, high coherence). Both systems have similarly high coherence but the identity values differ by a factor of nearly 7.

**Negative results:** This conclusion comes from two diagnostic examples, not a dedicated independence test across the full validated set. A formal cross-system independence test remains open.

**Prior art position:** The adaptive threshold formula in this patent treats I and rho as a coupled but distinct two-term multiplier. Existing methods either use a single signal property or combine multiple properties into one composite.

---

## Discovery 3: Linear Identity Modulation Over-Corrects in Extreme Collapse, Square Root Dampening is the Successful Correction

**Finding:** Multiplying identity by coherence linearly (I × rho) drives the threshold correction too far toward zero in extreme-collapse systems. Replacing it with the square root of identity (sqrt(I) × rho) preserves a meaningful correction even at very low identity values. The square root version is what makes detection work in the extreme-collapse regime.

**Problem it solves:** No principled way existed to scale adaptive threshold corrections in proportion to identity loss without over-correcting. Linear scaling becomes useless once identity drops to extreme values.

**Why it matters:** Over-correction in extreme collapse is the most damaging kind of failure: the system has actually deteriorated severely, but the threshold has been pulled so far toward zero that detection misses it. Square root dampening fixes exactly this case.

**Methodology:** Bearing2_4 from the XJTU-SY dataset had end-state identity I = 0.061 (extreme collapse). The validator was tested first with linear I × rho as the multiplier, then with sqrt(I) × rho. The full formula is: Threshold = 1 + alpha × (degradation_rate / 100) × sqrt(I) × rho, with alpha = 0.1 for bearings.

**Evidence:** With the linear multiplier on Bearing2_4: 0.061 × 0.988 = 0.060, which corresponds to a 94% reduction in the correction term. Result: F1 = 0.455 (detection failed). With the square root multiplier: sqrt(0.061) × 0.988 = 0.244, which corresponds to a 76% reduction. Result: F1 = 0.957 (detection succeeded). Same bearing, same data, same alpha. The only change was switching from linear to square root.

**Negative results:** Linear I × rho was tested first and failed. Identity velocity (the rate of change of identity, dI/dt) was also explored as an alternative but added no benefit beyond sqrt(I) and was abandoned.

**Prior art position:** Existing adaptive threshold methods use linear scaling or fixed thresholds. Square root identity dampening as a non-linear correction term is not documented in prior degradation detection literature.

---

## Discovery 4: An Empirical Rescue Boundary Near I ≈ 0.25 Exists for Bearings in the Tested Set

**Finding:** In the tested XJTU-SY bearing set, the framework rescues all 10 validated passing bearings whose end-of-life identity falls below approximately I = 0.25. Bearings whose identity is at or above this regime, or which lack a usable degradation signal in the data, do not show the same rescue benefit. The boundary is empirical, observed in the tested set rather than derived from theory.

**Problem it solves:** Before this finding, no signal existed to predict whether adaptive threshold correction would help on a given bearing before running the full validation. Practitioners had no way to know in advance which systems were candidates for the method.

**Why it matters:** Knowing the regime in advance allows selective deployment. A practitioner can compute identity from baseline and current measurements, see whether the system falls below the boundary, and only apply the correction where it is likely to work. Blanket deployment across all systems is not the right approach.

**Methodology:** All 15 XJTU-SY bearings were run through the Config 2 validator (square root dampening). End-of-life identity was computed for each bearing. The pass criterion was F1 >= 0.5.

**Evidence:** All 10 passing bearings have identity I below approximately 0.25, with values ranging from 0.044 (Bearing3_1) to 0.248 (Bearing1_4). The five failing bearings break into two categories: three failed because identity was too high for the correction to apply, one failed because no degradation signal existed in the underlying data, and one failed because the rescue regime did not extend to its specific case. The framework cannot create detection signal where the data itself contains none.

**Negative results:** Three of the five failing bearings failed because their identity was above the rescue boundary, confirming that the boundary is real and that the correction is not neutral when applied outside its effective range. One failure case had no usable degradation signal at all — a fundamental data limitation, not a formula limitation.

**Prior art position:** Claim 30 of the filed provisional patent explicitly states that identity values below approximately 0.25 indicate systems for which the method is effective. This makes it a filed claimed discovery rather than a post-hoc observation.

---

## Discovery 5: Domain-Specific Alpha is Required and Cannot Be Assumed

**Finding:** The coupling constant alpha (which controls the overall strength of the correction) is not a universal value. It must be tuned per physical domain. Bearings require alpha = 0.1. Batteries require alpha = 0.034. Using the wrong value actively harms detection performance.

**Problem it solves:** Before this finding, there was no guidance for how strong the identity-aware correction should be across different physical degradation mechanisms. A practitioner deploying the method to a new domain had no way to know whether the bearing alpha would carry over or whether it would break the method.

**Why it matters:** A wrong alpha value causes detection to fail. The initial battery experiments in the development of this framework used alpha = 1.0 (the same magnitude as some early bearing experiments). The result was a catastrophic drop in F1 across all three batteries, by 37 to 43 percentage points. The formula itself was not broken. The domain calibration was wrong.

**Methodology:** Battery tests were initially run with alpha = 1.0, then rerun with the corrected alpha = 0.034. Bearing tests used alpha = 0.1 throughout. Both alpha values are documented in the repo's validator and config files.

**Evidence:** With alpha = 1.0 on batteries: F1 dropped by 37 to 43 percentage points across all three batteries (B0005, B0006, B0018). With alpha = 0.034: F1 = 0.967 (B0005), F1 = 0.975 (B0006), F1 = 0.949 (B0018) — all three pass strongly. Bearing alpha = 0.1 was validated across all 10 passing bearings without modification.

**Negative results:** The alpha = 1.0 battery failure is a documented failure case in the repo, not hidden. It serves as a cautionary example that demonstrates exactly how the method can appear to fail when a calibration step is wrong, even when the underlying formula is correct.

**Prior art position:** The patent specifies alpha = 0.034 for batteries and alpha = 0.1 for bearings as domain-specific parameters. Existing degradation detection methods that use a single coupling constant across all applications do not address this requirement.

---

**Last Updated:** May 6, 2026
