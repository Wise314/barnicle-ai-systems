# Universal Stability Engineering — Scientific Discoveries

**Patent #6 — Method and System for Engineering System Stability Through Inverse Design, Closed-Loop Control, and Universal Multi-Domain Monitoring**

**Application Number:** 63/960,829 (filed January 15, 2026)

**Repository:** https://github.com/Wise314/universal-stability-engineering

**Paper:** [Zenodo DOI 10.5281/zenodo.20080228](https://doi.org/10.5281/zenodo.20080228)

---

## Scope Note

This repo is an engineering extension of thermodynamic-stability-prediction, not a new stability law. The 42-system count represents validation instances, not 42 unique independent systems, because the same physical systems appear in multiple tests (Universal Platform and Inverse Design use the same 11 systems). The AI class imbalance test produces Phi = 0.282 here versus Phi = 0.261 in thermodynamic-stability-prediction due to independent implementation with slightly different entropy values. Both classify as STABLE. The three operational modes — Closed-Loop Control, Inverse Design, Universal Platform — are the distinct scientific contribution of this repo relative to Patent #5.

---

## Discovery 1: The Phi Formula Supports Three Operationally Distinct Engineering Modes From One Equation and One Threshold

**Finding:** Phi = I x rho minus alpha x S with alpha = 0.1 and threshold 0.25 supports three distinct engineering uses without modification: Inverse Design (pre-deployment stability constraint), Closed-Loop Control (real-time monitoring with quantified advance warning), and Universal Platform (cross-domain stability assessment). Each mode addresses a different point in the system lifecycle using the same formula, the same constants, and the same threshold.

**Problem it solves:** Prior stability engineering treats fault detection, prognostics, and design margin analysis as separate problems requiring separate domain-specific tools. No unified framework existed that covered design, monitoring, and detection under one equation across multiple physical domains.

**Why it matters:** A single framework covering all three lifecycle phases means the same formula, the same team, and the same infrastructure can be applied from pre-deployment design through operational monitoring to failure detection. This is the headline engineering contribution of this repo relative to the core law established in thermodynamic-stability-prediction.

**Methodology:** Three validation scripts tested each mode independently. Closed-loop control: Phi computed at each timestep for 10 XJTU-SY bearings across full run-to-failure lifecycles. Warning at Phi below 0.30, critical at Phi below 0.25. Universal platform: Phi computed at end-of-life for 11 systems across 3 domains (4 bearings, 4 turbofans, 3 seismic events). Inverse design: I x rho computed for same 11 systems and compared against required constraint 0.25 plus alpha x S. Scripts: test_closed_loop_control.py, test_universal_platform.py, test_inverse_design.py.

**Evidence:** Closed-loop: 10/10 bearings detected before failure, average advance warning 86.1% of lifetime, minimum 73.2%. Universal platform: 11/11 systems correctly classified. Inverse design: 11/11 systems correctly classified, 1/1 stable systems met constraint, 10/10 failed systems violated constraint. The same physical systems appear across modes for overlapping subsets, but exact reported Phi values may differ slightly between scripts because the modes are implemented and summarized independently.

**Negative results:** The Universal Platform and Inverse Design tests use the same 11 systems so those two modes share their validation evidence. The Closed-Loop Control uses 10 bearing systems overlapping with the Universal Platform bearing subset. The three modes are not validated on independent system sets. This repo depends on the stability law established in thermodynamic-stability-prediction and should be framed as an engineering extension, not as if it independently discovered the cross-domain stability law.

**Prior art position:** The repo positions this as distinct from prior stability engineering approaches because it uses the same Phi framework for design constraints, monitoring, and cross-domain assessment without domain-specific retuning, whereas existing methods treat these functions separately with separate domain-specific tools.

---

## Discovery 2: Closed-Loop Phi Monitoring Provides a Quantified Advance Warning Window Averaging 86.1% of Bearing Lifetime

**Finding:** Continuous Phi monitoring during bearing operation triggered critical alerts (Phi below 0.25) with advance warning averaging 86.1% of total bearing lifetime across all 10 tested bearings, with a minimum of 73.2% on Bearing2_4, which had the shortest lifecycle at 42 timesteps. All 10 bearings were detected before failure at both the warning and critical thresholds.

**Problem it solves:** Traditional bearing monitoring detects failure imminence too late for planned maintenance. Prior work in this portfolio established that Phi drops below threshold before failure but did not quantify how early.

**Why it matters:** An average advance warning of 86.1% means operators typically have more than four-fifths of remaining bearing life to respond after the first critical alert in the tested set. The 73.2% minimum establishes a conservative lower bound for planning purposes. The result holds across lifecycles spanning more than one order of magnitude (42 to 2,538 timesteps), showing that the effect is not confined to one lifecycle length in the tested set.

**Methodology:** This repo quantifies advance warning as a percentage of total lifetime. Each of 10 XJTU-SY bearings run to failure with Phi computed at each timestep. Warning threshold set at Phi below 0.30, critical threshold at Phi below 0.25. Advance warning computed as (total lifecycle minus first alert timestep) divided by total lifecycle, expressed as percentage. Same 10 bearings as thermodynamic-stability-prediction repo. Script: test_closed_loop_control.py.

**Evidence:** Per-bearing critical advance warnings: Bearing1_1 82.0% (123 timestep lifecycle), Bearing1_2 90.0% (161), Bearing1_3 90.4% (158), Bearing1_4 83.5% (122), Bearing2_2 90.0% (161), Bearing2_3 90.0% (533), Bearing2_4 73.2% (42, minimum), Bearing2_5 82.2% (339), Bearing3_1 90.0% (2,538), Bearing3_4 90.0% (1,515). Average critical advance 86.1%. Average warning advance (Phi below 0.30) 86.6%, minimum 75.6%.

**Negative results:** The advance warning result is established on bearings only. Whether the same advance warning percentages hold for turbofans, grids, or seismic systems under continuous monitoring is not tested in this repo. Bearing2_4 has both the shortest lifecycle and the worst advance warning, which is a potential confound. The warning and critical thresholds were carried over from thermodynamic-stability-prediction and were not independently cross-validated on a held-out bearing set.

**Prior art position:** The repo positions this as distinct from domain-specific bearing prognostics methods because it provides advance warning as a percentage of total lifetime from a training-free thermodynamic metric without per-bearing calibration or failure history from the specific bearing.

---

## Discovery 3: The Inverse Design Constraint Correctly Partitioned Stable and Failing Systems Across Three Physical Domains

**Finding:** The constraint I x rho greater than 0.25 plus alpha x S, derived algebraically from the stability condition Phi greater than 0.25, correctly classified all 11 tested systems as stable or failing across seismic, mechanical, and aerospace domains. 10/10 failed systems violated the constraint. 1/1 stable system met it. The violation margin ranged from 0.008 (Engine_4, near-threshold) to 0.586 (Bearing1_2, extreme collapse).

**Problem it solves:** Stability monitoring is reactive, it detects instability during operation. No method existed to express a pre-deployment design requirement as a single algebraic constraint on observable system properties that applies across different physical domains without domain-specific models.

**Why it matters:** A designer can compute the required I x rho from expected entropy and use it as a stability specification before deployment. Engine_4 at margin 0.008 shows the constraint is sensitive enough to flag near-threshold systems rather than only catching extreme failures.

**Methodology:** For each of 11 systems, required I x rho computed as 0.25 plus 0.1 x S using the system's measured entropy. Actual I x rho computed from the same validation data as thermodynamic-stability-prediction. Systems where actual I x rho exceeds required classified MEETS (stable), all others VIOLATES (failure). 4 bearings (XJTU-SY), 4 turbofan engines (NASA C-MAPSS), 3 seismic cases (USGS Donna Lea strainmeter). Script: test_inverse_design.py.

**Evidence:** Stable_2010: I x rho = 0.881 vs required 0.555, MEETS, correctly STABLE. Parkfield M6.0: I x rho = 0.434 vs required 0.571, VIOLATES. San Simeon M6.5: I x rho = 0.415 vs required 0.581, VIOLATES. Bearing1_1: 0.084 vs 0.502, VIOLATES. Bearing1_2: 0.052 vs 0.638, VIOLATES, largest margin 0.586. Bearing2_3: 0.108 vs 0.728, VIOLATES. Bearing3_1: 0.043 vs 0.569, VIOLATES. Engine_1: 0.439 vs 0.485, VIOLATES. Engine_2: 0.435 vs 0.499, VIOLATES. Engine_3: 0.397 vs 0.496, VIOLATES. Engine_4: 0.476 vs 0.484, VIOLATES, narrowest margin 0.008. 11/11 correct.

**Negative results:** Only one stable system was tested in the inverse design validation. A false positive rate cannot be estimated from one stable case. All aerospace engines tested were near end of life — no mid-life or healthy aerospace systems were tested for correct stable classification on the constraint. The inverse design test uses the same data as thermodynamic-stability-prediction, not an independent design-time dataset.

**Prior art position:** The repo positions this as distinct from domain-specific design-for-reliability methods because it expresses a design stability constraint as a single algebraic inequality on observable system properties applicable across seismic, mechanical, and aerospace systems without domain-specific models or material-specific equations.

---

## Discovery 4: The Formula Extends to NLP, Medical, and Audio Domains via Task-Identity Confusion Matrix Correlation as the I Observable

**Finding:** Using Task-Identity confusion matrix correlation as the I component, Phi = I x rho minus alpha x S with alpha = 0.1 and threshold 0.25 correctly classified failure states in three new domains not validated in thermodynamic-stability-prediction: NLP text classification (Phi = 0.036, critical), medical diagnosis (Phi = 0.000, critical), and audio classification (Phi = -0.200, collapse). A fourth vision domain shift test (MNIST to Fashion-MNIST, Phi = -0.113) also classified correctly. All four tested cases produced correct classifications in the reported evaluations.

**Problem it solves:** thermodynamic-stability-prediction validated the formula on mechanical, electrical, aerospace, AI, and geophysical systems. Whether the same formula applies to text, audio, and medical AI domains was not established by that repo.

**Why it matters:** NLP, medical AI, and audio classification are major production ML deployment categories. The Task-Identity to Phi conversion creates a bridge between the behavioral drift framework (task-identity repo) and the thermodynamic stability framework (thermodynamic-stability-prediction repo), allowing one formula and one threshold to span both physical and behavioral substrate types.

**Methodology:** For each domain, a baseline model trained on balanced data, then a drifted model produced by degraded or imbalanced training. Task-Identity I computed as Pearson correlation of flattened confusion matrices between baseline and drifted model outputs. rho = 1.0 for all cases (batch inference, no temporal dynamics). S = Shannon entropy of output distribution. alpha = 0.1, threshold 0.25. NLP: 20 Newsgroups (1,181 train, 786 test documents). Medical: Wisconsin Breast Cancer (398 train, 171 test records). Audio: Free Spoken Digit (3,000 wav recordings). Vision: MNIST and Fashion-MNIST (140,000 images combined). Script: test_task_identity_phi_conversion.py.

**Evidence:** Vision domain shift: I = 0.049, rho = 1.000, S = 1.627 bits, Phi = -0.113, COLLAPSE, accuracy 93.6% to 13.2%, correct. NLP imbalanced fine-tuning: I = 0.036, rho = 1.000, S = 0.000 bits, Phi = 0.036, CRITICAL, collapsed to single class, correct. Medical malignant-only retraining: I = 0.000, rho = 1.000, S = 0.000 bits, Phi = 0.000, CRITICAL, accuracy 97.1% to 37.4%, correct. Audio catastrophic forgetting: I = 0.000, rho = 1.000, S = 2.002 bits, Phi = -0.200, COLLAPSE, accuracy 94.0% to 0.0%, correct. 4/4 correct.

**Negative results:** All four Task-Identity conversion tests are failure cases only. No stable cases were included for NLP, medical, or audio domains, so the false positive rate for these domains is not established. The NLP and medical cases show S = 0.000 bits, meaning entropy contributes nothing to the Phi calculation and classification is driven entirely by I. Whether the full three-component formula provides additional signal over I alone in behavioral domains is not tested. rho is fixed at 1.0 for all behavioral cases so the temporal coherence component is not exercised.

**Prior art position:** The repo positions this as distinct from domain-specific AI monitoring tools because it applies the same thermodynamic formula with the same threshold to text, audio, and medical AI behavioral drift using Task-Identity as a shared I observable, without domain-specific calibration for each new modality.

---

## Discovery 5: Financial AI Behavioral Drift Produces a Near-Zero Phi Value That Correctly Predicts Collapse of Minority Class Detection on 2.26 Million Real Loans

**Finding:** A loan default detection model trained on Lending Club data (2,260,701 loans, 2007-2018) produced Phi = -0.001 after behavioral drift that reduced default detection from 81.8% to 0.5%, correctly classified as collapse. The collapse signal is driven by per-class default Task-Identity collapsing to I = 0.000 while the paid class remained high at 0.949, demonstrating that aggregate identity scores can mask catastrophic minority class failures, consistent with the per-class finding in the task-identity repo.

**Problem it solves:** Financial AI monitoring typically tracks overall accuracy or aggregate performance metrics that remain stable while minority class detection collapses. A stability metric sensitive to per-class behavioral collapse detects what aggregate metrics miss.

**Why it matters:** The 99.4% degradation in default detection on 2.26M real loans, correctly captured by Phi = -0.001, connects the thermodynamic stability framework to the per-class behavioral drift finding in the task-identity repo through a common I component definition.

**Methodology:** Lending Club dataset 2007-2018 (2,260,701 loans total, filtered to 2,246,031 with complete data, test set 673,810 loans). Baseline model trained with balanced class weights achieving 81.8% default detection. Drifted model trained with reduced default sensitivity achieving 0.5% default detection. For this validation, I was taken from the per-class Task-Identity of the default class, which fell to 0.000, while overall paid-class identity remained high at 0.949. rho = 1.000, S = 0.008 bits, Phi = 0.000 x 1.000 minus 0.1 x 0.008 = -0.001. Audit: 17/17 checks passed. Script: test_financial_lending.py.

**Evidence:** Baseline default detection 81.8%. Drifted default detection 0.5%. Degradation 99.4%. Per-class default Task-Identity I = 0.000. Overall paid-class Task-Identity 0.949. Phi = -0.001. Prediction: COLLAPSE. Outcome: FAILURE. Correct.

**Negative results:** This is a single test case representing one specific type of drift, deliberate reduction of default sensitivity. Whether the formula detects other financial AI failure modes such as concept drift from economic regime changes or natural feature distribution shift is not established. The Phi value of -0.001 is very close to zero and the collapse classification is driven almost entirely by I = 0.000 rather than a meaningfully negative Phi signal.

**Prior art position:** The repo positions this as distinct from aggregate financial AI monitoring methods because it detects per-class behavioral identity collapse in credit-risk classification on 2.26M real loans using the same thermodynamic formula as physical system monitoring.

---

**Last Updated:** May 8, 2026
