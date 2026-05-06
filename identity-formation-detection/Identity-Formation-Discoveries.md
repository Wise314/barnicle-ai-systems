# Identity Formation Detection — Scientific Discoveries

**Patent #2 — Method and System for Predicting Neural Network Training Efficiency from Early Behavioral Identity Formation**

**Application Number:** 63/914,409 (filed November 18, 2025)

**Repository:** https://github.com/Wise314/identity-formation-detection

**Paper:** [Zenodo DOI 10.5281/zenodo.20057039](https://doi.org/10.5281/zenodo.20057039)

---

## Discovery 1: Early Behavioral Identity Formation Predicts Later Training Efficiency

**Finding:** Behavioral identity formation measured after just one epoch of training predicts how much additional learning remains before a model reaches mature behavior. Higher formation scores correspond to less remaining improvement needed and faster convergence.

**Problem it solves:** Architecture search and training-budget estimation usually require long training runs before efficiency differences become clear. No method existed to predict training cost from early-epoch behavior.

**Why it matters:** If training efficiency can be estimated from the first epoch, slow learners can be identified early and compute can be concentrated on efficient architectures, enabling 80-95% reduction in NAS compute cost.

**Methodology:** Tested 7 MLP architectures on MNIST (7,000 train / 3,000 test) and 7 MLP architectures on CIFAR-10 (50,000 train / 10,000 test, full dataset). For each architecture, formation score was measured at epoch 1 against a mature checkpoint (epoch 30-50), and correlated with later accuracy improvement and epochs needed to reach a target accuracy threshold.

**Evidence:** MNIST MLP correlation r = -0.780, p < 0.01, n = 7. CIFAR-10 MLP correlation r = -0.781, p < 0.01, n = 7. Difference: 0.001. Formation 0.167 needed more than 30 epochs. Formation 0.995-0.997 needed approximately 5 epochs.

**Negative results:** The earlier bad-architecture detection hypothesis failed. Formation did not reliably identify generic bad architectures. Only extreme under-parameterization consistently produced very low formation. The failed hypothesis redirected the work to the efficiency-prediction discovery.

**Prior art position:** The repo and provisional patent position this as distinct from standard architecture evaluation workflows because it uses early behavioral identity formation rather than full or near-full training to predict remaining training cost.

---

## Discovery 2: Behavioral Identity Forms Much Earlier Than Final Accuracy

**Finding:** In the tested MNIST MLP setting, behavioral identity forms almost immediately during training, well before final accuracy is reached. Formation reached 99.46% after just 1 epoch while accuracy was only 82%.

**Problem it solves:** Prior intuition assumed that behavioral structure emerges gradually with accuracy during training. No method distinguished the early formation of confusion structure from later performance refinement.

**Why it matters:** If identity forms early, then early training behavior contains predictive information about the rest of training. This is the prerequisite for all training efficiency prediction claims.

**Methodology:** MLP trained on MNIST to a mature checkpoint. Formation score computed by comparing confusion matrices at epochs 1, 2, 5, 10, 15, 20, 25, 30, 40, and 50 against the epoch-50 baseline using Task-Identity correlation. Diagnostic confusion matrix analysis directly compared epoch-1 and epoch-50 confusion matrices.

**Evidence:** Formation score at epoch 1: 0.9946. Formation score at epoch 50: 1.000. Accuracy at epoch 1: 82.1%. Accuracy at epoch 50: approximately 93%. Diagnostic confirmed same main confusion patterns (4 vs 9, 3 vs 8, 7 vs 1) at epoch 1 and epoch 50, differing only in magnitude.

**Negative results:** The original hypothesis predicted a gradual formation curve from 0 to 1.0 over training. That hypothesis was disproven. The near-complete formation at epoch 1 initially appeared to be a bug until diagnostic confusion matrix analysis confirmed it was real.

**Prior art position:** The repo frames this as a shift away from treating behavioral identity as a late-stage property of trained models. Confusion structure is largely established before final accuracy is achieved.

---

## Discovery 3: The MLP Formation-Efficiency Correlation Replicated Across Easy and Hard Datasets with Near-Identical Magnitude

**Finding:** The inverse correlation between formation score and training efficiency replicated to three decimal places across MNIST and CIFAR-10, suggesting the effect is not dataset-specific for MLPs.

**Problem it solves:** Single-dataset results can be dismissed as benchmark-specific artifacts. Cross-dataset replication is required for commercial and patent credibility.

**Why it matters:** Dataset-independent correlation means the method can be applied to new MLP settings without requiring validation on each new dataset.

**Methodology:** Exact replication of the MNIST MLP efficiency protocol on CIFAR-10 full dataset (50,000 train / 10,000 test, 32x32 color images, 10 classes). Same 7 MLP architectures. Same formation measurement at epoch 1 vs mature checkpoint. Same Pearson correlation calculation. A verification script was included to recompute saved correlations from disk.

**Evidence:** MNIST r = -0.780, R2 = 0.608, n = 7. CIFAR-10 r = -0.781, R2 = 0.609, n = 7. Difference: 0.001.

**Negative results:** None within the MLP cross-dataset replication. The clean replication did not automatically extend to every CNN setting.

**Prior art position:** The repo treats this as the strongest cross-dataset validation result for the method because the same relationship held on both an easy and a hard vision benchmark for the same architecture family.

---

## Discovery 4: Formation Score Does Not Reliably Screen Bad Architectures — Its Role is Efficiency Prediction

**Finding:** Formation score does not reliably identify generic bad architectures. Oversized and shallow architectures showed high formation scores (0.995 and 0.992) despite being part of the bad architecture test set. Only extreme under-parameterization consistently produced very low formation.

**Problem it solves:** The initial hypothesis was that low formation would identify bad architectures directly. That hypothesis needed to be explicitly tested and falsified before the real discovery could emerge.

**Why it matters:** This negative result sharpens the claim. The method predicts training efficiency, not architecture quality in general. Knowing this boundary prevents misuse.

**Methodology:** Tested MLP architectures labeled as good, tiny, very small, huge, and single-layer on MNIST. Compared epoch-1 formation scores and later performance across all architecture types.

**Evidence:** Tiny architecture (10-5 neurons): formation 0.167. Very small (20-10): formation 0.604. Huge (512-256-128): formation 0.995. Single layer (100): formation 0.992. Hypothesis rejected.

**Negative results:** This is the negative result. The bad-architecture screening hypothesis was explicitly rejected by the repo and documented as a failed test rather than hidden.

**Prior art position:** The repo treats this as an important falsified hypothesis that redirected the work from broad architecture-quality claims to the narrower and successful efficiency-prediction claim.

---

## Discovery 5: The Effect Extends to CNNs on Easy and Medium-Difficulty Datasets with a Hard-Dataset Boundary

**Finding:** Early formation predicted training efficiency strongly for CNNs on MNIST (r = -0.987) and Fashion-MNIST (r = -0.978), but the tested CNN settings on CIFAR-10 did not produce a usable correlation at epoch 1 or epoch 3.

**Problem it solves:** MLP validation alone does not address CNNs, which dominate production ML. Without CNN validation the method's commercial applicability was limited.

**Why it matters:** CNN validation on easy and medium-difficulty datasets covers the majority of commercial applications including medical imaging, satellite imagery, and industrial inspection. The documented CIFAR-10 failure defines an honest boundary rather than hiding a limitation.

**Methodology:** Tested 8 CNN architectures (24K to 1.1M parameters) on MNIST at epoch 1 (V7), Fashion-MNIST at epoch 1 (V9), CIFAR-10 at epoch 1 (V6), and CIFAR-10 at epoch 3 (V8). Formation correlated with later improvement in each setting.

**Evidence:** MNIST CNN r = -0.987, p < 0.01, n = 8. Formation range 0.610-0.999. Fashion-MNIST CNN r = -0.978, p < 0.01, n = 8. Formation range 0.790-0.971. CIFAR-10 epoch 1: r = +0.555, formation range too narrow at 0.657-0.772. CIFAR-10 epoch 3: r = -0.130, below significance threshold.

**Negative results:** Both CIFAR-10 CNN tests failed. At epoch 1, all CNN architectures learn similar low-level features on complex data, compressing formation scores into a narrow 0.115 range. Extending to epoch 3 did not resolve this.

**Prior art position:** The repo frames this as a bounded extension beyond MLPs with explicitly documented complexity limits rather than an unrestricted claim across all CNN tasks.

---

## Discovery 6: Early Formation Reveals Capacity-Threshold Structure in CNN Families

**Finding:** On MNIST CNNs, early formation scores clustered into three distinct groups that tracked architectural capacity thresholds, suggesting formation exposes meaningful regime changes in capacity that are not visible from architecture size alone.

**Problem it solves:** Architecture comparisons typically rely on size (parameter count) or final accuracy. This result suggests early formation may expose capacity regime transitions that neither metric reveals.

**Why it matters:** This may become a secondary scientific story on architectural phase transitions and capacity thresholds, even if it is not the repo's headline discovery.

**Methodology:** Compared 8 CNN architectures ranging from 2-layer to 4-layer convolutional stacks on MNIST. Examined epoch-1 formation scores and later improvement across architecture depth groups.

**Evidence:** 2-layer CNNs clustered at formation approximately 0.61, needing 55-57% improvement. 3-layer CNN at formation 0.961, needing 21% improvement. 4-layer CNNs at formation approximately 0.998-0.999, needing only 3-6% improvement.

**Negative results:** This cluster structure was documented on MNIST CNNs only. The repo does not show the same threshold structure holds across all datasets or architectures.

**Prior art position:** The repo presents this as a secondary insight emerging from the CNN validation experiments rather than the central patent claim.

---

**Last Updated:** May 6, 2026
