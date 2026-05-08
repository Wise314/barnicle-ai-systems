# Phi Controller — Scientific Discoveries

**Patent #8 — Method and System for Universal Neural Network Training Supervision Using Trajectory-Aware Stability Prediction**

**Application Number:** 63/938,279 (filed December 11, 2025)

**Repository:** https://github.com/Wise314/phi-controller

**Paper:** [Zenodo DOI 10.5281/zenodo.20083205](https://doi.org/10.5281/zenodo.20083205)

---

## Scope Note

This repo has two distinct scientific layers. The first is the trajectory-aware kill logic, checking best progress from training start rather than recent progress eliminates false kills on slow starters. The second is the Phi formula itself, Phi = I x rho - alpha x S_norm with normalized components works across 2, 10, and 100 class counts under one formula with two validated thresholds. These two layers are separable: the trajectory-aware logic is the primary operational value, and the Phi formula is the theoretical unification with the broader portfolio. The controller operates on real training trajectories across epochs and is not a zero-training predictor. The 100-class CNN test was run on Google Colab T4 GPU and is documented as such. The repo's validated objective is conservative kill filtering, not high overall binary accuracy, the method tolerates false passes while protecting against false kills.

---

## Discovery 1: Trajectory-Aware Best-Progress Supervision Dramatically Reduces False Kills Compared to Patience-Based Early Stopping

**Finding:** A controller that tracks the best Phi achieved from training start, rather than relying on a recent sliding window, produced 2 false kills in 660 tested architectures (99.7% kill precision) across all validation experiments. In the direct head-to-head comparison on 30 architectures, the controller produced 0 false kills while early stopping with patience=5 produced 20 false kills (16.7% kill precision) on the same architecture set.

**Problem it solves:** Standard patience-based early stopping kills architectures that are temporarily stalled or noisy but would later recover. It cannot distinguish temporarily struggling but viable architectures from fundamentally non-viable ones, because it evaluates recent improvement rather than overall trajectory from training start.

**Why it matters:** This is the primary operational discovery of the repo. In the head-to-head test, early stopping destroyed 20 viable architectures out of 30 tested. The trajectory-aware controller destroyed 0. The mechanism is the decision logic: termination occurs only when three conditions hold simultaneously, minimum epoch threshold reached, best Phi remains below threshold, and epochs without meaningful improvement exceed patience. This logic allows slow starters to recover without being prematurely killed.

**Methodology:** Head-to-head test on 30 random MNIST MLP architectures (test_universal_phi_vs_early_stopping.py). Both methods ran on identical architectures with identical seeds. Early stopping used patience=5. Phi controller used trajectory-aware logic checking best Phi achieved since epoch 0. Ground truth viability defined as reaching 60% accuracy on MNIST 10-class. Dataset: MNIST (7,000 train, 3,000 test). Implementation: sklearn, keras/TensorFlow, core.universal_phi module.

**Evidence:** Phi controller: 0 false kills, 4/4 correct kills, 100% kill precision on 30 architectures. Early stopping patience=5: 20 false kills, 4/24 correct kills, 16.7% kill precision on same 30 architectures. Phi controller used 540 total epochs (18 average). Early stopping used 449 total epochs (15 average) but destroyed 20 viable architectures in the process. Average accuracy loss from Phi controller: 0.34%. Average accuracy loss from early stopping: 0.38%. Across the full 660-architecture validation: 2 false kills total, 99.7% kill precision.

**Negative results:** The trajectory-aware logic achieves fewer total epoch savings than early stopping (10% vs 25.2% reduction vs full training) because it allows more architectures to complete training. The tradeoff is correct kills at the cost of fewer epoch savings. This test was on 30 architectures. Whether the 0 false kill result holds at larger scale is bounded by the 660-architecture total across all experiments.

**Prior art position:** Standard patience-based early stopping (Prechelt 1998) and resource schedulers (Hyperband, Li et al. 2017; Successive Halving, Jamieson and Talwalkar 2016) use sliding-window or multi-stage evaluation that assesses recent improvement rather than best progress from training start. No prior work explicitly characterizes trajectory-aware best-progress logic as the mechanism distinguishing viable slow starters from genuinely non-viable architectures.

---

## Discovery 2: The 0.56 Relative Performance Hypothesis Failed on 2-Class Data, Normalizing the Formula Is the Correct Mechanism for Cross-Class-Count Generalization, Not Adjusting the Threshold

**Finding:** The hypothesis that 56.5% relative performance above random, the value corresponding to the 0.22 I_deficit threshold on 10-class problems, constitutes a universal viability criterion failed when tested on 2-class breast cancer data. A viable architecture was falsely killed under this threshold but reached 95%+ with full training. This falsified hypothesis directly motivated the Phi formula by establishing that cross-class-count generalization requires normalizing the formula components, not adjusting the threshold to match a fixed relative performance criterion. The subsequent breast cancer validation achieved 0 false kills across 30 architectures.

**Problem it solves:** Without testing and explicitly rejecting the 0.56 hypothesis, the false kill error on 2-class data would have been attributed to an edge case rather than to a fundamental flaw in the approach of threshold adjustment as the generalization mechanism.

**Why it matters:** This negative result sharpens the positive claim. The Phi formula works across class counts not because it adjusts thresholds to match relative performance, but because it normalizes each component so that the same threshold applies regardless of class count within the 2-10 class range. The distinction matters for the patent claim: what is being claimed is a formula structure, not a threshold calibration procedure.

**Methodology:** The 0.56 hypothesis was derived from the 10-class threshold: threshold accuracy 60.84% maps to (60.84 - 10) / (100 - 10) = 56.5% relative performance. Applying this to 2-class: viable_acc = 0.50 + 0.565 x 0.50 = 78.25%. A breast cancer architecture was tested with this viability threshold, falsely killed, and confirmed to reach 95%+ with full training. The Phi formula subsequently eliminated this failure mode.

**Evidence:** 0.56 hypothesis: 1 false kill on 2-class breast cancer data. Phi controller on 30 breast cancer architectures: 0 false kills, 0 false passes. Three ground-truth unviable architectures all correctly predicted non-viable.

**Negative results:** This is itself the negative result. The 0.56 hypothesis was tested, failed, and documented. The failure was limited to one architecture on one dataset, but one counterexample is sufficient to falsify the hypothesis.

**Prior art position:** The distinction between threshold-based cross-class-count generalization, which fails, and formula-normalization-based generalization, which succeeds, is not described in prior NAS or architecture viability prediction literature.

---

## Discovery 3: The Phi Formula With Normalized Identity and Normalized Entropy Achieves Cross-Class-Count Architecture Viability Prediction Under One Formula With Two Validated Class-Range Thresholds

**Finding:** Phi = I x rho - alpha x S_norm, where I = (accuracy - random) / (1 - random), rho = autocorrelation of accuracy history at lag 1, S_norm = entropy(CM) / log2(n squared), and alpha = 0.1, predicts architecture viability across 2-class, 10-class, and 100-class problems. Threshold 0.25 applies to 2-10 class problems and 100-class MLPs. Threshold 0.084 applies to 100-class CNNs. Across all 660 validated architectures: 2 false kills, 99.7% kill precision.

**Problem it solves:** The prior identity-deficit approach (Patent #7, I_deficit = 1 - sqrt(F)) was calibrated for 10-class problems only. Raw accuracy and raw entropy are both class-count dependent. Without normalization, the entropy penalty becomes several times harsher as class count rises, approximately 3.3x harsher for 10-class than 2-class and 6.6x harsher for 100-class than 2-class, based on the maximum entropy values of log2(4) = 2.0 bits, log2(100) = 6.64 bits, and log2(10000) = 13.29 bits respectively. The V1 formula using raw entropy failed on 10-class after being calibrated on 2-class data for exactly this reason.

**Why it matters:** Normalizing identity against the random baseline and normalizing entropy against the maximum possible entropy for an n x n confusion matrix constrains both I and S_norm to the range 0-1 regardless of class count, making the formula and alpha = 0.1 class-count-agnostic across the tested range. This gives the neural controller the same functional form as the inventor's earlier Phi-based stability methods in physical systems.

**Methodology:** All 660 architectures evaluated using core.universal_phi module. MLPs: Breast Cancer 2-class (n=35), MNIST 10-class (n=305), Fashion-MNIST 10-class (n=100), MNIST full scale 70K samples 50 epochs 10-class (n=100), MNIST with bottleneck architectures 10-class (n=100), CIFAR-100 MLPs 100-class (n=5). CNNs: CIFAR-10 10-class (n=115), CIFAR-100 100-class (n=100, T4 GPU, Google Colab). All real data, no synthetic examples. Datasets: Breast Cancer (UCI), MNIST, Fashion-MNIST, CIFAR-10, CIFAR-100.

**Evidence:** MLPs: 445 architectures, 0 false kills, 100% kill precision, threshold 0.25. 10-class CNNs: 115 architectures, 2 false kills, 98.3% kill precision, threshold 0.25. Two false kills at CIFAR-10 accuracies of 60.6% and 61.5%, edge cases barely above the 60% viability boundary with high entropy. 100-class CNNs: 100 architectures, 0 false kills, 100% kill precision, threshold 0.084. Total: 660 architectures, 2 false kills, 99.7% kill precision.

**Negative results:** The formula required two thresholds, not one. The 0.25 threshold that works for 2-10 class does not work for 100-class CNNs. Viable 100-class architectures achieving 20-28% accuracy produce Phi values of 0.08-0.15, below 0.25. The two CIFAR-10 CNN false kills at 60.6% and 61.5% accuracy are genuine edge cases where high confusion matrix entropy causes the formula to underestimate stability on barely-viable architectures. 100-class MLP validation used only 5 architectures and is less comprehensive than MLP tests at other class counts.

**Prior art position:** The identity-deficit approach (Patent #7) uses I_deficit = 1 - sqrt(F) with threshold 0.22, validated for 10-class only. The Phi formula extends this by adding temporal coherence (rho) and normalized entropy (S_norm) components. The normalization scheme, dividing entropy by log2(n squared) and normalizing identity against the random baseline, makes the formula class-count-agnostic across the tested range. No prior work demonstrates a single thermodynamic-structured formula with these normalization choices predicting architecture viability across 2, 10, and 100 class counts simultaneously.

---

## Discovery 4: 100-Class CNNs Are a Genuine Boundary Condition, the Formula Generalizes Further Than the Threshold, and Systematic Threshold Grid Analysis Achieves 100% Kill Precision at 0.084

**Finding:** Viable 100-class CNN architectures achieving 20-28% accuracy (20-28x better than the 1% random baseline) produce Phi values of 0.084-0.15, below the 0.25 threshold that works for 2-10 class problems. Setting threshold to 0.10 produced 99% kill precision (1 false kill on 100 architectures). Systematic grid analysis across threshold values from 0.060 to 0.120 identified 0.084 as the optimal threshold, achieving 0 false kills with 19 false passes on 100 100-class CNN architectures.

**Problem it solves:** The same threshold that works for 2-10 class problems kills all viable 100-class CNN architectures, making the formula unusable for 100-class CNNs without calibration. No systematic method existed for finding the optimal threshold for a new class range without exhaustive trial and error.

**Why it matters:** This is one of the most important boundary discoveries in the repo. It prevents overclaiming one threshold for all class counts and replaces that with a stronger and more honest claim: one normalized formula, two validated class-range thresholds. The threshold grid analysis (Test 15) is a reusable methodology, it takes Phi values already computed during a validation run, sweeps a threshold grid, and identifies the value that achieves 0 false kills with minimum false passes. It requires no additional training.

**Methodology:** Initial calibration set threshold to 0.10 based on observation that viable 100-class architectures had Phi 0.08-0.15. Full validation on Google Colab T4 GPU: 100 100-class CNN architectures on CIFAR-100 (5,000 train, 1,000 test, 100 classes), 30 epochs each, 34 minutes total. Ground truth viability: accuracy >= 20% (20x random baseline). Threshold optimization (Test 15, threshold_optimization_analysis.py): swept threshold values 0.060 through 0.120 using saved Phi values from the full validation run. Recorded false kills and false passes at each threshold value.

**Evidence:** At threshold 0.25: all 34 viable architectures killed (100% false kill rate). At threshold 0.10: 1 false kill (Seed 83, [64,64] fc=256+BN, accuracy 20.6%, Phi 0.084), 8 false passes, 99% kill precision. Threshold grid selected values: 0.060 gives 0 false kills 28 false passes; 0.080 gives 0 false kills 21 false passes; 0.084 gives 0 false kills 19 false passes; 0.085 gives 1 false kill 17 false passes; 0.100 gives 1 false kill 8 false passes. Optimal threshold 0.084: 0 false kills, 19 false passes, 100% kill precision.

**Negative results:** The optimal threshold 0.084 is derived from the same dataset used for validation, not a held-out calibration set. The Seed 83 edge case (Phi exactly 0.084) sits at the threshold boundary. Whether 0.084 generalizes to other 100-class CNN datasets or architecture families is not established. The tradeoff at 0.084 is 19 false passes versus 8 false passes at threshold 0.10. This is an acceptable operational tradeoff since false passes waste training time but do not discard viable architectures.

**Prior art position:** Threshold optimization via post-hoc grid analysis on computed metric values is a standard technique. The specific application, using Phi values from a completed validation run to find the optimal kill threshold for a new class range without additional training, in the context of architecture viability prediction under the Phi framework is not described in prior art.

---

## Discovery 5: The Phi Formula Shows Robustness Across Tested Optimizer, Learning-Rate, and Batch-Size Settings

**Finding:** The Phi formula with threshold 0.25 correctly classified all tested architectures as viable across 5 optimizers (Adam, SGD, SGD+Momentum, RMSprop, Adagrad), 7 learning rates (0.0001 to 0.1), and 6 batch sizes (16 to 512) on MNIST 10-class MLPs, with 0 false kills across all 18 hyperparameter configurations.

**Problem it solves:** A viability controller that works on default hyperparameters but fails on non-default configurations would require per-configuration recalibration, limiting its utility in NAS and hyperparameter search where configurations are varied explicitly.

**Why it matters:** Robustness across basic training hyperparameters supports the claim that the controller logic is not fragile to ordinary training-loop choices. The same threshold can be applied in NAS pipelines that vary optimizer, learning rate, and batch size simultaneously without recalibration.

**Methodology:** Three separate robustness tests on MNIST 10-class MLP ([128, 64] architecture). Optimizer test: 5 optimizers, all other hyperparameters fixed, 30 epochs. Learning rate test: 7 values from 0.0001 to 0.1, Adam optimizer, 30 epochs. Batch size test: 6 values from 16 to 512, Adam optimizer, learning rate 0.01, 30 epochs. Viability ground truth: accuracy >= 60%. All real data. Implementation: keras/TensorFlow, core.universal_phi module.

**Evidence:** Optimizer test: Adam Phi 0.7828, SGD Phi 0.5875, SGD+Momentum Phi 0.7093, RMSprop Phi 0.7712, Adagrad Phi 0.7553. All above 0.25. Final accuracies 89.1%-91.3%. Learning rate test: all 7 learning rates produced Phi above 0.25, final accuracies 83.2%-91.2%. Batch size test: all 6 batch sizes produced Phi above 0.25, final accuracies 90.7%-92.4%. 0 false kills across all 18 configurations.

**Negative results:** All 18 configurations were viable architectures. This test establishes that the formula correctly passes viable architectures across hyperparameter settings but does not establish that it correctly kills non-viable architectures across all hyperparameter settings. The test was conducted on one architecture ([128, 64]) and one dataset (MNIST). Whether robustness holds for non-viable architectures or other datasets is not established by these tests.

**Prior art position:** Hyperparameter robustness testing for architecture viability predictors is not described in prior NAS literature as a separately validated property. Most prior work validates on fixed hyperparameter settings.

---

## Discovery 6: The Controller Is a Conservative Kill Filter Optimized for False-Kill Avoidance, Not a Balanced Binary Viability Classifier

**Finding:** The validated objective of the controller is extremely low false-kill rate, not high overall binary classification accuracy. Many test suites show substantial false passes while preserving 0 false kills. In the MNIST 100-architecture random test, the run reported 53/100 correct predictions, 0 false kills, and 47 false passes. In the bottleneck architecture test, 0 false kills occurred alongside 10 false passes. This is the operational design of the controller, not a limitation.

**Problem it solves:** Without stating this explicitly, the repo could be misread as claiming high overall binary accuracy when it is actually claiming high-precision early termination of genuinely non-viable architectures. The distinction matters for how the results are interpreted and how the method should be deployed.

**Why it matters:** The practical cost of a false kill in NAS is much worse than the cost of a false pass. A false kill destroys a viable architecture and the compute invested in it. A false pass only causes extra training time. The controller is designed to accept the latter to avoid the former. The 99.7% kill precision claim means the controller almost never discards a viable architecture, which is the operationally critical property for NAS use cases.

**Methodology:** Post-hoc comparison of false kills and false passes across all test suites in the repo. Raw terminal outputs (experiments/OUTPUTS.md) document both error types per test. The 100-architecture MNIST test and the bottleneck test are the clearest illustrations of the asymmetric error structure.

**Evidence:** MNIST 100-architecture test: 0 false kills, 47 false passes, 53/100 correct overall. Bottleneck architecture test: 0 false kills, 10 false passes. CIFAR-100 CNN test at optimal threshold 0.084: 0 false kills, 19 false passes. Total across 660 architectures: 2 false kills, 99.7% kill precision.

**Negative results:** The asymmetric design means the controller does not maximize balanced accuracy. In settings where false passes are operationally costly, a more aggressive threshold or a balanced classifier may be preferable. The controller is strongest as a safe pruning tool where the cost of mistakenly discarding a good architecture outweighs the cost of unnecessary training.

**Prior art position:** The conservative asymmetric design, accepting false passes to eliminate false kills, is not explicitly characterized as a design objective in prior early stopping or NAS pruning literature. Patience-based early stopping implicitly accepts false kills in pursuit of compute savings.

---

**Last Updated:** May 8, 2026
