# Universal Phi ML — Scientific Discoveries

**Patent:** US Provisional Application No. 63/956,800
**Filed:** January 9, 2026
**Title:** Method and System for Machine Learning Using Training-Free Stability Metrics as Input Features
**Repository:** https://github.com/Wise314/universal-phi-ml
**Paper:** [10.5281/zenodo.20093635](https://doi.org/10.5281/zenodo.20093635)

---

## Scope

This repo builds a second intellectual property layer on top of the Phi framework. The core question is whether the components of Phi (I, rho, S) can serve as input features to ML systems that predict qubit quality across IBM Quantum hardware. The repo contains two layers of evidence. The original 8 tests (January 2, 2026) had a circularity problem: Phi appeared in both the feature set and the label definition, making results tautological. The strict 8 tests (January 7, 2026) corrected this by removing Phi from features, using backend-split evaluation, and reporting balanced accuracy. All headline discoveries use strict test results. The original tests are documented as exploratory context only. All data is real IBM Quantum calibration data from 445 qubits across three backends. No synthetic data.

---

## Discovery 1: The Phi Components (I, rho, S) Predict Phi-Threshold Class Across IBM Quantum Backends at 98.4% Balanced Accuracy Without Phi in the Feature Set

**Finding:** Using only the three components I, rho, and S as features, with Phi excluded to avoid circularity, strict backend-split classifiers achieve approximately 98.4% average transfer balanced accuracy, with within-backend performance at or near 100% in the strict summaries. This is the strongest non-circular result in the repo.

**Problem it solves:** Prior qubit quality classifiers either require per-backend training or include Phi in the feature set, which is circular since Phi is computed from the same components. The cited repo materials do not identify prior work showing that the raw components alone generalize across IBM hardware without retraining.

**Why it matters:** The relationship between I, rho, and S and the Phi-threshold stability classification is stable across hardware. A model trained on ibm_fez deploys on ibm_torino and ibm_marrakesh without retraining. This establishes the core non-circular generalization claim for Patent #16.

**Methodology:** Backend-split evaluation. Train on one of three IBM backends, test on the other two. Features: I = (fidelity - 0.50) / 0.50, rho = T2/T1, S = readout error. Phi excluded from feature set. Labels: Phi >= 0.25 (disclosed). Balanced accuracy as primary metric. Four ML model types tested. 445 qubits, ibm_fez, ibm_torino, ibm_marrakesh. All real IBM calibration data, no synthetic examples.

**Evidence:** Average transfer balanced accuracy 98.4%. Average within-backend balanced accuracy 100%. Transfer drop 1.6 percentage points. Per-direction results: ibm_fez to ibm_torino 100.0%, ibm_fez to ibm_marrakesh 98.4%, ibm_torino to ibm_fez 97.2%, ibm_torino to ibm_marrakesh 95.2%, ibm_marrakesh to ibm_fez 99.6%, ibm_marrakesh to ibm_torino 100.0%.

**Negative results:** The labels are defined by Phi >= 0.25, so this test establishes that components predict the Phi-threshold consistently across backends, not that the threshold itself is independently validated by ML. That validation is provided by circuit execution results in the quantum-phi-validation repo. Some backend-specific variation exists, as shown by the 95.2% minimum transfer direction.

**Prior art position:** In the cited prior work discussed here, IBM proprietary calibration systems use ML models trained on platform-specific data. The cited repo materials do not identify a component-based classifier achieving 98.4% balanced accuracy across IBM backends without retraining and without using Phi as a feature.

---

## Discovery 2: Rho (T2/T1) Is the Dominant Predictor Component, Accounting for 70 to 78% of Feature Importance, While I and S Alone Are Near Random for This Classification Task

**Finding:** Under strict backend-split feature ablation with Phi excluded, rho alone achieves 94.6% balanced accuracy. I alone achieves 52.2% (near random). S alone achieves 50.9% (near random). Adding I to rho improves to 98.6%. Adding S to I plus rho gives 98.4%. Feature importance across model types assigns 70 to 78% weight to rho, 12 to 16% to I, and 9 to 15% to S.

**Problem it solves:** Prior qubit selection heuristics, including mapomatic (Nation and Treinish 2023), explicitly excluded T2/T1 from cost functions. This result quantifies exactly how much predictive work each component contributes and establishes that for predicting the Phi-threshold class under the strict backend-split setup, I and S alone carry little independent signal without rho.

**Why it matters:** Rho doing 70 to 78% of the predictive work is the central mechanistic finding. I plus rho without S marginally outperformed the full set in the strict feature-ablation average (98.6% vs 98.4%), so S did not improve average balanced accuracy in this classification setting. This finding is consistent with the r = 0.9458 correlation between Phi and T2/T1 found in the quantum-phi-validation repo. Both repos independently confirm the coherence ratio is the load-bearing component.

**Methodology:** Strict feature ablation using backend-split evaluation. Five feature sets tested: rho alone, I alone, S alone, I plus rho, I plus rho plus S. Phi excluded from all. Balanced accuracy as primary metric. Feature importance computed across Random Forest and Gradient Boosting models.

**Evidence:** rho alone 94.6%, I plus rho 98.6%, I plus rho plus S 98.4%, I alone 52.2%, S alone 50.9%. Feature importance: rho 70 to 78%, I 12 to 16%, S 9 to 15%.

**Negative results:** I alone at 52.2% and S alone at 50.9% are near the balanced accuracy baseline for binary classification with imbalanced classes. This weakens the original framing that the Phi formula beats its parts. The strict story is that rho dominates and I plus rho is the best feature set for this classification task.

**Prior art position:** Nation and Treinish (2023) excluded T2/T1 from qubit selection cost functions. This ablation result quantifies the consequence: approximately 70 to 78% of the predictive signal is discarded when T2/T1 is excluded from consideration.

---

## Discovery 3: Multiple ML Model Families Work Under Strict Evaluation, With Tree-Based Models Strongest and SVM Weakest

**Finding:** Under strict methodology, four model types produce the following transfer balanced accuracy: Gradient Boosting 98.7%, Random Forest 98.4%, Neural Network 98.1%, SVM 82.2%. All four exceed 80% average balanced accuracy, confirming the I, rho, S feature set is model-agnostic, though SVM shows the highest variance across backend pairs.

**Problem it solves:** If only one model type worked, the finding would be model-specific rather than a property of the features. Model-agnosticism supports the claim that the component structure carries real signal rather than being exploitable only by one architecture.

**Why it matters:** Tree-based models perform best, but even SVM at 82.2% average exceeds the minimum useful threshold. The spread from 82.2% to 98.7% shows that model choice matters at the margin but does not determine whether the approach works at all.

**Methodology:** Same strict backend-split evaluation as Discovery 1. Four model types trained and tested independently on the same 445 qubit dataset.

**Evidence:** Gradient Boosting 98.7% (min 95.2%, max 100.0%), Random Forest 98.4% (min 95.2%, max 100.0%), Neural Network 98.1% (min 95.2%, max 100.0%), SVM 82.2% (min 52.8%, max 100.0%).

**Negative results:** SVM shows substantially higher variance than tree-based models, with a minimum transfer balanced accuracy of 52.8% on one backend pair direction. SVM exceeds 80% on average but is less reliable than the other three model types for cross-backend deployment.

**Prior art position:** The cited repo materials do not identify a training-free component-based feature set validated across four model architectures with cross-backend evaluation on real IBM quantum hardware.

---

## Discovery 4: Pair Quality Defined by the Minimum-Phi Rule Transfers Across Backends at 98.2% Accuracy Under Strict Evaluation

**Finding:** Under strict backend-split evaluation, pair-quality prediction using the min-Phi rule achieves 98.2% average cross-backend accuracy. In the original exploratory tests, min-Phi outperformed avg-Phi (86.5%) and max-Phi (69.2%) for both classification and regression, establishing the weakest-link interpretation. The strict test confirms the transfer result holds without relying on the circular original methodology.

**Problem it solves:** Multi-component systems need an aggregation rule. The weakest-link principle establishes how to extend single-qubit Phi scores to pair-level quality prediction in a way that generalizes across hardware.

**Why it matters:** The minimum-Phi rule is not just empirically useful on one backend, it transfers. This connects directly to circuit compilation: when mapping logical qubits to physical qubit pairs, the pair quality is bounded by the weaker of the two qubits.

**Methodology:** Strict Test 2. For each qubit pair, component features from both qubits (I1, rho1, S1, I2, rho2, S2) used as inputs. Labels defined by min-Phi >= 0.25. Backend-split evaluation. 442 pairs from 445 qubits across three backends.

**Evidence:** Strict average transfer accuracy 98.2%. Per-direction range: 96.1% to 100.0%. Original exploratory comparison: min-Phi 100%, avg-Phi 86.5%, max-Phi 69.2% (original methodology, circular, provided as context only).

**Negative results:** The direct comparison of min vs avg vs max Phi aggregation rules comes from the original tests which included Phi as a feature and used random train/test split. The strict test confirms cross-backend transfer for min-Phi pair labels but does not repeat the three-way comparison under strict methodology.

**Prior art position:** The cited repo materials do not identify a weakest-link component-based pair classifier validated with cross-backend evaluation on real IBM quantum hardware without using Phi as a feature.

---

## Discovery 5: Three-Way Classification (GOOD, MARGINAL, BAD) Achieves 91.7% Balanced Accuracy Across Backends Using (I, rho, S) Features Under Strict Evaluation

**Finding:** Under strict backend-split three-way classification using GOOD (Phi >= 0.25), MARGINAL (0.15 <= Phi < 0.25), and BAD (Phi < 0.15) as labels, the classifier achieves 98.2% average transfer accuracy and 91.7% average transfer balanced accuracy. The lower balanced accuracy reflects class imbalance, with GOOD qubits substantially more common than MARGINAL and BAD qubits, making the minority classes harder to classify consistently across backends.

**Problem it solves:** Binary classification loses the distinction between marginal qubits that warrant monitoring and bad qubits that should be excluded. Three-way classification supports a stricter three-way ML partition of qubit quality states.

**Why it matters:** 91.7% balanced accuracy for a three-class imbalanced problem under backend-split is a strong result. The full Phi grading system can be approximated by an ML model trained on one backend and deployed on another without retraining.

**Methodology:** Strict Test 7. Three classes defined by Phi boundaries. Backend-split evaluation. I, rho, S features only. Balanced accuracy and accuracy both reported.

**Evidence:** Average transfer accuracy 98.2%, average transfer balanced accuracy 91.7%.

**Negative results:** The three-way result is weaker than the two-way result (91.7% vs 98.4% balanced accuracy) and should be framed as a supporting finding rather than a headline result. The class thresholds are derived from Phi-space, so this is an ML deployment discovery rather than an independent physical discovery. Note that the BAD class boundary here (Phi < 0.15) differs from the patent classification where BAD is defined as Phi < 0.

**Prior art position:** The cited repo materials do not identify three-way qubit quality classification using training-free component features with cross-backend evaluation on IBM quantum hardware.

---

## Discovery 6: T2 Coherence Time Is Moderately Predictable From (I, rho, S) Across Backends (R² = 0.32), but T1 Relaxation Time Carries No Cross-Backend Signal (R² = -0.28)

**Finding:** Under strict backend-split multi-target regression using only I, rho, and S as features, T2 coherence time achieves R² = 0.32 (moderate signal). T1 relaxation time achieves R² = -0.28, indicating the relationship does not generalize across backends.

**Problem it solves:** Establishes the scope of what the I, rho, S feature set can and cannot predict beyond the Phi-threshold classification task.

**Why it matters:** The T2 moderate signal confirms the feature set carries real physical information about coherence quality. The T1 null result is the cleaner scientific finding: T1 contains backend-specific information that I, rho, S cannot capture, establishing a real physical boundary on the framework.

**Methodology:** Strict Test 6. Backend-split regression. Features: I, rho, S only. Targets: T2 and T1 separately. R² computed on held-out backends.

**Evidence:** T2 average R² = 0.32 (moderate). T1 average R² = -0.28 (no cross-backend signal).

**Negative results:** The T2 prediction has partial circularity because rho = T2/T1 is a direct function of T2. Some of the R² = 0.32 is explained by this structural overlap rather than genuine independent prediction. The T1 null result has no circularity concern and is the cleaner of the two. Claim 19 (multiple output predictions) is only partially supported under strict evaluation because T1 does not generalize.

**Prior art position:** The cited repo materials do not identify cross-backend prediction of T2 and T1 separately from normalized calibration components on IBM quantum hardware, including the explicit T1 null result.

---

## Discovery 7: For T2-Based Quality Prediction, a Data-Driven Threshold of 0.60 Outperforms the Physics-Derived 0.25 by 16 Percentage Points Under Strict Evaluation

**Finding:** When the prediction task is classifying qubits by T2 quality (T2 >= median T2 as ground truth) rather than by Phi-threshold, a data-driven threshold of 0.60 achieves 78.0% balanced accuracy while the physics-derived threshold of 0.25 achieves 61.8% balanced accuracy (rank 8 of 11 tested values). The gap is 16.2 percentage points.

**Problem it solves:** Whether the physics-derived 0.25 threshold is optimal for every downstream prediction task. This result shows that the disclosed Phi_c = 0.25 classification boundary is not overturned, but for the narrower task of T2-quality prediction specifically, a data-driven threshold near 0.60 performs better.

**Why it matters:** The result shows that the physics-derived 0.25 threshold is not optimal for every downstream task. For T2-based quality prediction specifically, a data-driven threshold near 0.60 improves balanced accuracy by 16.2 percentage points. This validates the two-layer model: Layer 1 (computing Phi with threshold 0.25) and Layer 2 (using Phi components in ML with task-specific threshold optimization) are distinct and the second layer adds measurable value.

**Methodology:** Strict Test 8. Threshold sweep across 11 values. Ground truth: T2 >= median T2 across the dataset. For each threshold, a classifier is trained using that threshold to define labels on the training backend, then tested on held-out backends. Balanced accuracy as primary metric.

**Evidence:** Threshold 0.60: 78.0% balanced accuracy (optimal). Threshold 0.55: 77.6%. Threshold 0.50: 76.1%. Threshold 0.25: 61.8% (rank 8 of 11). Improvement from 0.25 to 0.60: 16.2 percentage points.

**Negative results:** The 0.60 result is specific to T2-quality prediction. Whether 0.60 is optimal for other prediction targets is not established. The 78.0% accuracy for the optimal threshold still leaves substantial room for improvement. The partial circularity of rho containing T2 information means the T2 prediction result overestimates true independent predictive power.

**Prior art position:** The cited repo materials do not identify data-driven threshold optimization for T2-based qubit quality classification using training-free component features under cross-backend evaluation, with a documented 16 percentage point improvement over a physics-derived baseline.

---

## Methodological Note: Original Tests Had Circularity and Were Superseded by Strict Tests

The original 8 tests (January 2, 2026) included Phi in the feature set while Phi also defined the classification labels, creating a tautology. Results including Phi alone = 100% accuracy, cross-backend transfer = 99.8%, three-way classification = 100%, and fidelity R² = 99.96% are preserved in the repo history but should not be cited as primary scientific evidence. The fidelity R² = 99.96% in particular is a direct mathematical consequence of I being computed from fidelity and is not an independent discovery. The strict tests (January 7, 2026) corrected the methodology and produced the discoveries compiled above. For the main cross-backend binary classification tasks, performance remained in the high-90% range after the strict methodology removed circularity, showing that the underlying signal is real. However, the circularity materially weakened several original claims, especially those involving Phi alone, formula-vs-components comparisons, and fidelity prediction. The strict tests should therefore be treated as the primary scientific evidence, with the original tests retained only as exploratory context.

---

## Summary

| Discovery | Key Result |
|-----------|-----------|
| 1. Cross-backend classification | 98.4% balanced accuracy without retraining |
| 2. Rho dominance | 70 to 78% feature importance, ρ alone 94.6% |
| 3. Model-agnostic features | All 4 models exceed 80%, GB best at 98.7% |
| 4. Pair quality transfer | 98.2% accuracy via min-Phi rule |
| 5. Three-way classification | 91.7% balanced accuracy (GOOD/MARGINAL/BAD) |
| 6. Multi-target regression | T2 R²=0.32, T1 R²=-0.28 (cross-backend null) |
| 7. Threshold optimization | 0.60 beats 0.25 by 16pp on T2-quality task |

---

**Patent:** US Provisional Application No. 63/956,800
**Filed:** January 9, 2026
**Paper DOI:** [10.5281/zenodo.20093635](https://doi.org/10.5281/zenodo.20093635)
**Repository:** https://github.com/Wise314/universal-phi-ml
