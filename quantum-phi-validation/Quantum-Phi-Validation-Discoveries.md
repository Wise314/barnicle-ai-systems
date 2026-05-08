# Quantum Phi Validation — Scientific Discoveries

**Patent:** US Provisional Application No. 63/952,883
**Filed:** January 2, 2026
**Title:** Method and System for Quantum Sensor Stability Monitoring Using Universal Thermodynamic Identity Framework
**Repository:** https://github.com/Wise314/quantum-phi-validation
**Paper (v2):** [10.5281/zenodo.20088933](https://doi.org/10.5281/zenodo.20088933)
**Paper (v1):** [10.5281/zenodo.18522745](https://doi.org/10.5281/zenodo.18522745)

---

## Scope

This repo validates the Universal Phi framework on real IBM Quantum hardware across 445 qubits on three backends (ibm_fez, ibm_torino, ibm_marrakesh). All results are from real calibration data and circuit execution on IBM hardware. No synthetic data. Of the 13 substantive validation tests, 10 validate Phi, 1 is a weak positive, and 2 are inconclusive but not contradictory. The temporal early warning result is bounded by a 30-day collection window with daily snapshots.

---

## Discovery 1: The Phi Formula Compresses Fidelity, Coherence, and Readout Error Into a Single Scalar That Tracks Qubit Quality Across 445 Real Qubits on Three IBM Backends

**Finding:** Phi = I x rho - alpha x S, where I = (fidelity - 0.50) / 0.50, rho = min(T2/T1, 1.0), S = readout error, and alpha = 0.1, produces a Pearson correlation of r = 0.9458 with the coherence ratio T2/T1 across 445 qubits on ibm_fez (156 qubits), ibm_torino (133 qubits), and ibm_marrakesh (156 qubits). Low-Phi qubits show materially worse calibration metrics than high-Phi qubits: 4.3x shorter T2, 3.7x lower T2/T1, and 2.4x higher readout error.

**Problem it solves:** Prior qubit selection heuristics, including the mapomatic approach (Nation and Treinish 2023), explicitly excluded T2/T1 coherence ratio information from cost functions because it did not materially affect single-snapshot layout ordering. To our knowledge, the cited prior work does not describe a single-scalar training-free metric that combined fidelity, coherence, and readout error into one value predictive of qubit stability.

**Why it matters:** The r = 0.9458 correlation confirms that Phi captures qubit quality as a single interpretable scalar from calibration data alone, requiring no training. The dominant role of the coherence ratio (70 to 78% of component contribution) shows that T2/T1, previously excluded from heuristic qubit selection, is the central signal in a longitudinal stability-monitoring context as opposed to a single-snapshot layout-ordering context.

**Methodology:** Calibration snapshots collected from IBM Quantum backends. For each qubit: F = 1 - single_qubit_gate_error, I = (F - 0.50) / 0.50, rho = min(T2/T1, 1.0), S = readout_error, Phi = I x rho - 0.1 x S. Pearson correlation computed between Phi and T2/T1 across all 445 valid qubits. Feature ablation used backend-split train/test evaluation with Phi excluded from features to avoid circularity. Four ML model types tested (Random Forest, Gradient Boosting, Neural Network, SVM) with balanced accuracy as primary metric. All real calibration data, no synthetic examples.

**Evidence:** r = 0.9458 (n = 445). Feature ablation: rho alone 94.6% balanced accuracy, I + rho 98.6%, I + rho + S 98.4%, I alone 52.2%, S alone 50.9%. Cross-backend transfer: 98.4% balanced accuracy training on one backend and testing on others, versus 99.1% within-backend (0.7 percentage point reduction). Calibration group comparison: low-Phi qubits (Phi < 0.25) mean T2 = 30.9 microseconds, T2/T1 = 0.204, readout error = 0.064. High-Phi qubits (Phi >= 0.25) mean T2 = 132.0 microseconds, T2/T1 = 0.749, readout error = 0.027.

**Negative results:** The high correlation between Phi and T2/T1 means Phi is primarily measuring what T2/T1 already measures. The independent contribution of I and S is real but modest. The feature ablation characterizes component contribution within the metric rather than providing fully independent validation of the formula structure. Whether the formula adds meaningful predictive value over T2/T1 alone in all qubit selection settings is not established.

**Prior art position:** Nation and Treinish (2023) explicitly excluded T2/T1 from their qubit selection cost function. IBM proprietary calibration systems use ML models trained on platform-specific data that cannot transfer across vendors. No prior work demonstrates a single training-free formula combining I, rho, and S with this specific normalization and coupling constant, validated with 98.4% cross-backend transfer accuracy.

---

## Discovery 2: All 5 Dead Qubits in the Tested Dataset Were Correctly Identified by Phi < 0 With Zero False Negatives

**Finding:** Five qubits with fidelity = 0.000 (completely failed) produced Phi < 0 in the tested dataset. All five were correctly classified as BAD under the negative-Phi criterion. Zero failed qubits produced Phi >= 0 in the tested sample. The negative-Phi classification arises structurally from the formula: when fidelity falls below the 0.50 random baseline for a two-level system, I = (F - 0.50) / 0.50 becomes negative, and Phi = I x rho - alpha x S is negative regardless of rho and S values.

**Problem it solves:** Dead qubits produce erroneous results on any circuit. Without a pre-execution screening criterion, circuits assigned to dead qubits fail silently.

**Why it matters:** The negative-Phi criterion provides a natural failure indicator with no additional threshold to calibrate. The sign of Phi changes automatically when fidelity crosses the random baseline. This is a structural property of the formula rather than an empirically tuned boundary.

**Methodology:** All 445 qubits from ibm_fez, ibm_torino, and ibm_marrakesh analyzed. Qubits with calibration-reported fidelity of 0.000 identified. Phi computed for all qubits. Sign of Phi compared to failure status.

**Evidence:** 5 dead qubits identified: all produced Phi < 0. Zero false negatives in the tested dataset. Specifically: ibm_fez Q72 (Phi = -0.0343), ibm_torino Q53 (Phi = -0.0431), ibm_marrakesh Q82 (Phi = -0.0333), Q113 (Phi = -0.0264), Q119 (Phi = -0.0212).

**Negative results:** This result is bounded to the 5 dead qubits present in the 30-day collection window. The negative-Phi criterion identifies currently dead qubits, not qubits about to fail. Whether the criterion produces false positives (qubits with Phi < 0 that are not actually dead) was not separately characterized across all conditions.

**Prior art position:** Dead qubit detection is typically handled through calibration-reported status flags. The negative-Phi criterion provides a physics-grounded indicator arising from the formula structure rather than a separately tuned detection mechanism.

---

## Discovery 3: Minimum Phi of the Qubit Pair Predicts Two-Qubit Gate Reliability With 4.34x Higher Error When Min-Phi Falls Below 0.25

**Finding:** For 1,004 two-qubit gates on IBM hardware, gates where the minimum Phi of the constituent qubit pair fell below 0.25 produced mean error of 7.90% versus 1.82% for gates where minimum Phi was at or above 0.25, a 4.34x discrimination ratio. The operative rule is that two-qubit gate reliability is bounded by the weaker qubit in the pair, making minimum Phi the correct aggregate for gate-level prediction.

**Problem it solves:** Two-qubit circuit execution is open-loop: qubits are selected, the circuit runs, and errors are measured only at completion. To our knowledge, the cited prior work does not describe a training-free scalar metric for predicting which qubit pairs would produce elevated gate error before execution.

**Why it matters:** This extends Phi from single-qubit scoring into actionable gate-level reliability prediction. The weakest-link rule (minimum Phi across participating qubits) is both physically motivated and empirically validated on 1,004 real gates.

**Methodology:** Two-qubit gate analysis on ibm_fez. For each gate pair, minimum Phi computed from the two constituent qubit Phi values. Gates grouped by whether minimum Phi fell below or at or above 0.25. Mean error compared across groups. All real hardware execution, no simulation.

**Evidence:** 1,004 gates analyzed. Min-Phi < 0.25: mean error 7.90% (220 gates). Min-Phi >= 0.25: mean error 1.82% (784 gates). Discrimination ratio 4.34x.

**Negative results:** The minimum-Phi rule captures qubit-level stability contributions to gate error. It does not capture gate-specific error sources (pulse calibration, cross-talk) that can vary independently of single-qubit Phi. Bell-state validation was inconclusive because one high-Phi pair still showed poor Bell fidelity due to bad two-qubit gate quality independent of single-qubit Phi values.

**Prior art position:** Nation and Treinish (2023) used a multiplicative cost function over individual error rates for layout ordering. The minimum-Phi rule uses a single scalar threshold on a normalized formula rather than a multiplicative product of separate error metrics.

---

## Discovery 4: Low-Phi Qubits Produce 25 to 63x Higher Circuit Execution Error and 8 to 18x Discrimination Across Depths From 10 to 500 Gates

**Finding:** Circuit execution on low-Phi qubits produced 25 to 63x higher error than high-Phi qubits across circuit depths from 10 to 200 gates. A dedicated depth-scaling experiment showed 8 to 18x consistent discrimination across depths from 10 to 500 gates. Stress tests (T-gate repeated 24 times and heavy identity 200 gates) produced 16x to effectively infinite higher error for low-Phi qubits.

**Problem it solves:** A calibration metric only has operational value if it predicts real execution outcomes. Circuit error must be validated directly on hardware rather than inferred from calibration alone.

**Why it matters:** This closes the loop from calibration metrics to actual execution behavior. The depth-scaling consistency (8 to 18x from 10 to 500 gates) is particularly important because it shows the discrimination does not collapse as circuit complexity increases.

**Methodology:** Deep circuit test: 10 qubits selected as low-Phi and high-Phi, identity-return circuits of depths 10 to 200 gates executed on real IBM hardware, error measured as 1 - P(0). Depth scaling: 20 qubits at 10 depths from 10 to 500 gates. Stress tests: T-gate T^24 and 200-gate heavy identity circuits on low-Phi and high-Phi qubits. All on real IBM hardware.

**Evidence:** Deep circuit: depth 10 ratio infinite (0.00% vs 1.14%), depth 50 ratio 63x (0.02% vs 1.26%), depth 100 ratio 25x (0.04% vs 1.02%), depth 200 ratio 60x (0.02% vs 1.20%). Depth scaling: 8.94x at 10 gates, 9.63x at 100, 12.33x at 200, 17.60x at 400, 8.13x at 500. Stress tests: T-gate low-Phi 1.08% vs high-Phi 0.00% (infinite), heavy identity low-Phi 1.28% vs high-Phi 0.08% (16x).

**Negative results:** The simple single-gate circuit test was deprecated because single-gate circuits are too fast and too shallow for coherence effects to dominate. That test was replaced by the deep-circuit test. Variational circuit validation was inconclusive with small effect sizes, suggesting Phi is a weaker predictor for variational workloads where entangling gate noise can dominate over single-qubit coherence effects.

**Prior art position:** Nation and Treinish (2023) demonstrated approximately 40% fidelity recovery through post-compilation routing. The present result shows much larger discrimination ratios on identity and stress circuits using a single pre-execution scalar Phi without routing optimization.

---

## Discovery 5: In the Tested ibm_fez Selection Experiment, Phi-Based Qubit Selection Reduced Circuit Error by 83% Compared to Worst-Case Selection and Outperformed Random Selection by 74%

**Finding:** Selecting the top 20 qubits by Phi from a pool of 60 on ibm_fez produced mean circuit error of 0.22%, compared to 1.30% for the bottom 20 qubits (5.99x improvement, 83.3% error reduction) and 0.82% for a random 20-qubit selection (3.77x improvement, 73.6% reduction). High-Phi qubits had mean Phi 0.999, random selection had mean Phi 0.753, and low-Phi qubits had mean Phi 0.156.

**Problem it solves:** Quantum circuit compilers must map logical qubits to physical qubits without a principled pre-execution quality metric. Default assignments and heuristic cost functions do not account for single-qubit stability.

**Why it matters:** An 83% error reduction from qubit selection alone, requiring only a scalar ranking of existing calibration data, is an immediately deployable compiler optimization. It requires no circuit modification, no routing changes, and no training data.

**Methodology:** 60 qubits evaluated from ibm_fez calibration data. Ranked by Phi. Bottom 20 (worst), random 20, and top 20 (best) groups selected. Identical identity-return circuits executed on each group. Error measured as mean circuit execution error across all qubits in each group.

**Evidence:** Best 20: mean error 0.22%, mean Phi 0.999. Random 20: mean error 0.82%, mean Phi 0.753. Worst 20: mean error 1.30%, mean Phi 0.156. Best vs worst: 5.99x improvement, 83.3% error reduction. Best vs random: 3.77x improvement, 73.6% reduction.

**Negative results:** This test was conducted on ibm_fez only. Whether the same absolute error reduction holds on ibm_torino and ibm_marrakesh is not established by this specific test. Cross-backend validation shows Phi discriminates on all three backends but at different magnitudes (2.5x to 16x), so the 83% figure is backend-specific.

**Prior art position:** Mapomatic (Nation and Treinish 2023) achieves approximately 40% fidelity recovery through post-compilation routing. The present result achieves 83% error reduction through pre-execution Phi ranking alone, applied before compilation, without post-compilation optimization.

---

## Discovery 6: Phi Discriminates GHZ Entanglement Quality at 4.42x Error Ratio for Low-Phi Versus High-Phi Qubit Triplets

**Finding:** GHZ entanglement validation on 5 low-Phi qubit triplets versus 5 high-Phi qubit triplets produced 18.74% versus 4.24% entanglement error, a 4.42x discrimination ratio. The minimum Phi across the three qubits in each triplet was the operative selection criterion. Threshold 0.25 correctly separated the low and high groups.

**Problem it solves:** Multi-qubit entangled state preparation is sensitive to the weakest qubit in the participating set. No training-free scalar metric existed for predicting entanglement quality from calibration data before circuit execution.

**Why it matters:** This extends the single-qubit and two-qubit Phi findings into a multi-qubit entanglement setting. The minimum-Phi rule generalizes from gate pairs to triplets, supporting the entanglement path selection claims in the patent.

**Methodology:** GHZ circuits prepared on 5 qubit triplets with minimum Phi below 0.25 and 5 qubit triplets with minimum Phi at or above 0.25. GHZ fidelity measured from real IBM hardware execution. Error computed as 1 - fidelity.

**Evidence:** Low-Phi triplets: mean GHZ fidelity 81.26%, error 18.74%. High-Phi triplets: mean GHZ fidelity 95.76%, error 4.24%. Ratio 4.42x.

**Negative results:** Bell-state validation was inconclusive because two-qubit gate quality can vary independently of single-qubit Phi. One high-Phi pair still showed poor Bell fidelity due to bad two-qubit gate calibration. This establishes a real boundary condition: Phi measures single-qubit stability, and gate-specific error sources are not fully captured by single-qubit Phi values. The GHZ entanglement claim should be kept bounded to the tested GHZ triplet setting rather than extended to all multi-qubit entanglement tasks.

**Prior art position:** Entanglement path selection in prior quantum compiler literature uses heuristic multi-metric cost functions and does not use a single training-free stability scalar for path quality prediction.

---

## Discovery 7: Phi Serves as a Leading Indicator of Qubit Degradation With Average Lead Time of 6.8 Days in the 30-Day Tested Window

**Finding:** In a 30-day longitudinal study (December 31, 2025 through January 29, 2026) collecting 19 daily calibration snapshots across 445 qubits on three IBM backends, Phi below a warning threshold of 0.12 preceded IBM-reported qubit degradation events with 100% detection rate across all 52 degradation events. Of those 52 events, 11 produced Phi warnings at least 24 hours in advance (21.2% on-time recall), with average lead time of 163 hours (6.8 days) and maximum lead time of 480 hours (20 days). This result should be framed as a leading indicator, not a deterministic predictor.

**Problem it solves:** IBM Quantum's monitoring approach confirms qubit functionality reactively through calibration cycles but provides no advance warning of degradation. Workloads are disrupted reactively when qubits fail rather than rerouted proactively in advance.

**Why it matters:** When Phi does provide early warning, the lead times are operationally meaningful. An average of 6.8 days allows planned workload rerouting and scheduled recalibration. The 20-day maximum (qubit 98 on ibm_fez) demonstrates the signal can precede failure by weeks in some cases.

**Methodology:** Daily Phi snapshots collected using daily_phi_collection.py. Warning threshold Phi_warn = 0.12. Degradation event defined as IBM calibration-reported operational status transition indicating qubit unavailability or failure. Lead time defined as time between first warning snapshot and degradation event. On-time defined as lead time at least 24 hours. 445 qubits, 3 backends, 19 snapshots over 30 days. All real IBM calibration data, no synthetic examples.

**Evidence:** Total degradation events: 52. Detection rate: 100% (zero missed). On-time recall: 21.2%, corresponding to 11 events with at least 24 hours of lead time. Same-snapshot rate: 64.1%, reflecting the daily sampling resolution reported in the temporal analysis. False positive rate: 18.8%. Precision: 47.8%. For the 11 on-time events, lead times ranged from 29.61 hours to 479.97 hours, with an average of 163 hours (6.8 days).

**Negative results:** The 21.2% on-time recall is the honest operational number. 64.1% of detections occurred in the same snapshot as the degradation event because snapshots were taken only once per day. Whether those 33 same-snapshot cases would have shown lead times with higher-frequency sampling (2 to 4 times daily) is plausible but not tested. The study covers 30 days with daily sampling, providing 52 total degradation events. Extended collection of 60 to 90 days and more frequent snapshots are needed to characterize the signal more completely. Precision of 47.8% means more than half of Phi warnings did not result in observed degradation within the tested window.

**Prior art position:** IBM Quantum's documented monitoring approach is reactive. Nation and Treinish (2023) addressed single-snapshot layout optimization with no temporal component. To our knowledge, the cited prior work does not describe a single training-free scalar metric providing multi-day advance warning of IBM Quantum qubit degradation from calibration metrics alone with the specific lead time distribution reported here.

---

## Discovery 8: Threshold 0.25 Sits in a Strong Empirically Supported Discrimination Plateau in the Tested IBM Setting and Transfers Across Three IBM Backends Without Recalibration

**Finding:** Threshold sensitivity analysis across 445 qubits shows the 0.25 threshold sits in an optimal discrimination plateau: threshold 0.15 gives 4.74x T2 discrimination, threshold 0.25 gives 4.26x, and threshold 0.35 gives 3.34x. The same formula and threshold validated on ibm_fez, ibm_torino, and ibm_marrakesh with backend-level error discrimination ratios of approximately 16x, 2.5x, and 5.75x respectively, without backend-specific recalibration.

**Problem it solves:** A threshold used without sensitivity analysis could be a fragile fit rather than a stable operating point. Cross-backend transfer without recalibration is required for the method to be deployable on new hardware.

**Why it matters:** The threshold sits in a real empirical discrimination plateau rather than at a knife-edge optimum, supporting 0.25 as a stable operating threshold rather than an artifact of one specific calibration snapshot. Cross-backend transfer at 98.4% balanced accuracy without retraining establishes that the method does not require per-backend fitting.

**Methodology:** Threshold sweep: T2/T1 ratio computed for low-Phi versus high-Phi qubit groups at threshold values 0.15, 0.25, and 0.35. Cross-backend: same formula and threshold applied to ibm_fez, ibm_torino, and ibm_marrakesh. Backend-level low-vs-high Phi error ratios computed from cross-backend validation test.

**Evidence:** Threshold sweep: 0.15 gives 4.74x, 0.25 gives 4.26x, 0.35 gives 3.34x. Cross-backend discrimination: ibm_fez 16x, ibm_torino 2.5x, ibm_marrakesh 5.75x. Cross-backend balanced accuracy: 98.4% transfer versus 99.1% within-backend.

**Negative results:** The threshold 0.15 gives slightly higher T2 discrimination than 0.25 in the threshold sweep. The honest claim is that 0.25 is a physically motivated and empirically supported plateau value, not the single highest-discrimination threshold by every metric. The cross-backend transfer accuracy of 98.4% reflects modest backend-specific variation, a 0.7 percentage point reduction from within-backend accuracy.

**Prior art position:** Platform-specific quantum monitoring systems require per-backend training or calibration. The present result establishes that a fixed threshold of 0.25 transfers across three IBM backends with less than one percentage point accuracy reduction, supporting training-free deployment on new hardware.

---

## Boundary and Negative Findings

These results are reported as boundary conditions or inconclusive findings and are part of the scientific record.

**Bell-state validation was inconclusive.** Two-qubit gate quality can vary independently of single-qubit Phi. One high-Phi pair showed poor Bell fidelity due to bad two-qubit gate calibration. This establishes that Phi measures single-qubit stability and does not fully capture gate-specific error sources.

**Variational circuit validation was inconclusive with small effect sizes.** Entangling gate noise may dominate over single-qubit coherence effects in variational workloads, making Phi a weaker predictor in that setting.

**Error correction showed a weak positive result of 1.22x improvement** in logical error rate using three-qubit bit-flip codes with Phi-selected qubits. This is a supporting finding only, not a headline result.

**The simple single-gate circuit validation test was deprecated** because single gates are too shallow and too fast for coherence effects to dominate. It was replaced by the deep-circuit test and is not counted as an independent validation result.

**Of 13 substantive validation tests, 10 validate Phi, 1 is a weak positive, and 2 are inconclusive but not contradictory.**

---

## Summary

| Discovery | Key Result |
|-----------|-----------|
| 1. Single-scalar qubit quality | r = 0.9458 with T2/T1, 70-78% rho contribution |
| 2. Dead qubit detection | 5/5 correctly identified, zero false negatives |
| 3. Two-qubit gate prediction | 4.34x error ratio across 1,004 gates |
| 4. Circuit execution discrimination | 8-18x consistent across 10-500 gate depths |
| 5. Phi-based qubit selection | 83% error reduction on ibm_fez |
| 6. GHZ entanglement | 4.42x error ratio for triplets |
| 7. Early warning of degradation | 6.8 day average, 20 day max lead time |
| 8. Threshold plateau and cross-backend | 98.4% transfer accuracy, no retraining |

---

**Patent:** US Provisional Application No. 63/952,883
**Filed:** January 2, 2026
**Paper v2 DOI:** [10.5281/zenodo.20088933](https://doi.org/10.5281/zenodo.20088933)
**Repository:** https://github.com/Wise314/quantum-phi-validation
