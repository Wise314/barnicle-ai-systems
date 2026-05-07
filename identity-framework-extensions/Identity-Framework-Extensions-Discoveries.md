# Identity Framework Extensions — Scientific Discoveries

**Patent #3 — Method and System for Predicting Neural Network Transfer Learning Performance**

**Application Number:** 63/920,092 (filed November 18, 2025)

**Repository:** https://github.com/Wise314/identity-framework-extensions

**Paper:** [Zenodo DOI 10.5281/zenodo.20067239](https://doi.org/10.5281/zenodo.20067239)

---

## Discovery 1: Domain Shift Type Determines the Prediction Regime

**Finding:** The same zero-shot metric (confusion matrix diagonal strength) changes predictive role depending on the type of domain shift. Structure-preserving geometric shifts (rotations, blur) fall into a binary prediction regime where high diagonal predicts positive transfer. Structure-destroying degradations (noise, contrast reduction) fall into a magnitude prediction regime where low diagonal predicts larger transfer benefit.

**Problem it solves:** Without a unifying explanation, opposite correlation directions look contradictory and easy to dismiss as benchmark noise. Prior work had no mechanism explaining when and why a transfer predictor should be interpreted as a binary selector versus a magnitude estimator.

**Why it matters:** This is the mechanism-level discovery that makes all downstream transfer learning results scientifically coherent. It explains why one predictor can support two different downstream decisions depending on shift type, and defines the boundary conditions for each.

**Methodology:** Across all validated tests, rotations and blur were treated as structure-preserving geometric transforms, while Gaussian noise, salt-pepper noise, and contrast reduction were treated as degradations. In all cases the predictor was zero-shot confusion matrix diagonal strength F = trace(CM) / sum(CM) computed on the target domain before fine-tuning. Outcome variable was transfer advantage defined as fine-tuned accuracy minus scratch accuracy under matched training budgets. Datasets: MNIST, Fashion-MNIST (sklearn), Lending Club 2013-2016.

**Evidence:** Binary regime: MNIST rotations r = +0.445, p = 0.000006, n = 96; MNIST blur r = +0.495, p = 0.012, n = 25; Fashion-MNIST blur r = +0.564, p = 0.003, n = 25. Magnitude regime: MNIST Gaussian r = -0.941, p < 0.00001, n = 25; Fashion-MNIST Gaussian r = -0.786, p = 0.000003, n = 25; MNIST salt-pepper r = -0.730, p = 0.000034, n = 25; MNIST contrast reduction r = -0.573, p = 0.003, n = 25.

**Negative results:** Fashion-MNIST rotations did not produce a usable success-prediction regime (all negative transfer). CIFAR-10 Gaussian showed a real correlation (r = -0.478, p = 0.016) but diagonal range of 0.119 was insufficient for a strong claim. Fashion-MNIST salt-pepper missed significance at p = 0.053. KMNIST to MNIST failed due to near-zero feature overlap (diagonal range = 0.026, n = 5). Two critical bugs were found and corrected before any results were filed: the initial test gave fine-tuning twice the training budget of scratch training, and the original domain pair (MNIST to Fashion-MNIST) was one where transfer structurally cannot help. Honest win/loss ratio across all attempted tests: 6 wins, 4 losses (60%).

**Prior art position:** Prior work typically asks whether transfer helps in a given benchmark. This discovery adds a shift-type taxonomy that explains when the same zero-shot predictor should be interpreted as a binary selector versus a magnitude estimator. That framing is not present in prior transfer learning prediction literature.

---

## Discovery 2: Zero-Shot Diagonal Strength Predicts Transfer Success on Geometric Shifts Across Architecture Scale

**Finding:** Zero-shot confusion matrix diagonal strength predicts whether transfer learning will help on structure-preserving geometric shifts, and this effect holds independently within every tested architecture size from 16 to 384 neurons (24x range), with all 8 architectures individually significant.

**Problem it solves:** Transfer learning usually requires expensive fine-tuning to discover whether a source model is worth using. In the tested rotation setting only 8.3% of transfers produced positive results, meaning 91.7% of compute is wasted without prediction. A predictor that only works for specific model sizes would have limited production utility.

**Why it matters:** Architecture-independence means a practitioner can apply the predictor once across all candidate model sizes without recalibrating. This is what makes the 92% compute savings figure meaningful across heterogeneous model collections.

**Methodology:** Source models trained on clean MNIST or Fashion-MNIST, evaluated zero-shot on rotated or blurred target variants. Diagonal strength computed from zero-shot target confusion matrix. Outcome was transfer advantage relative to matched scratch training. MNIST rotation tests: 8 MLP architecture sizes (Tiny 16,8 through Massive 384,192) across 12 rotation angles (15 to 180 degrees), n = 96. MNIST blur and Fashion-MNIST blur: 5 architectures across 5 blur kernel sizes, n = 25 each.

**Evidence:** MNIST rotations: r = +0.445, p = 0.000006, n = 96. MNIST blur: r = +0.495, p = 0.012, n = 25. Fashion-MNIST blur: r = +0.564, p = 0.003, n = 25. Per-architecture rotation correlations: Medium r = 0.822 (p = 0.0010), Small r = 0.822 (p = 0.0010), Large r = 0.789 (p = 0.0023), Tiny r = 0.758 (p = 0.0043), Medium-Large r = 0.685 (p = 0.0140), Huge r = 0.676 (p = 0.0158), Very Large r = 0.626 (p = 0.0293), Massive r = 0.585 (p = 0.0458). All 8 individually significant. Transfer success rate in rotation tests: 8/96 (8.3%) positive, 88/96 (91.7%) negative.

**Negative results:** A slight weakening of correlation at larger architectures (Massive r = 0.585 vs Medium r = 0.822) is a real trend. The pooled r of 0.445 is lower than individual per-architecture correlations because pooling across architectures with different base accuracies adds variance. The per-architecture significance is the stronger evidence for universality. Fashion-MNIST rotations failed entirely as a useful binary validation regime, while Fashion-MNIST blur succeeded.

**Prior art position:** Architecture-independent prediction from zero-shot diagonal strength alone, validated across a 24x parameter range, is not established in prior transfer learning prediction literature.

---

## Discovery 3: Magnitude Prediction Holds Across a Complete Degradation Taxonomy

**Finding:** On degraded-data tasks, lower zero-shot diagonal strength predicts a larger transfer learning benefit rather than outright success or failure, and this inverse pattern holds across additive noise (Gaussian), impulse noise (salt-pepper), and quality degradation (contrast reduction), completing a full degradation taxonomy.

**Problem it solves:** In many real deployments the question is not simply whether transfer helps but how much benefit pre-training will provide as data quality worsens. A method that only covers one noise type cannot be deployed confidently in settings where degradation character is varied or unknown.

**Why it matters:** This turns the predictor into a resource-allocation tool. It can prioritize transfer learning where degraded data will benefit most and avoid over-investing where transfer provides only marginal gains. Completing the taxonomy across three structurally distinct degradation types makes the claim defensible as a general principle.

**Methodology:** Five architectures (Small to Very Large) trained on clean source data, evaluated zero-shot and fine-tuned on degraded target data across five severity levels per degradation type. MNIST Gaussian noise (sigma = 0.1 to 0.5), Fashion-MNIST Gaussian noise (same range), MNIST salt-pepper noise (density = 0.05 to 0.25), MNIST contrast reduction (scale = 0.2 to 1.0). Transfer advantage computed as fine-tuned minus scratch at each severity level and architecture combination.

**Evidence:** Additive noise: MNIST Gaussian r = -0.941, p < 0.00001, n = 25, all 25 positive transfers; Fashion-MNIST Gaussian r = -0.786, p = 0.000003, n = 25, 23/25 positive. Impulse noise: MNIST salt-pepper r = -0.730, p = 0.000034, n = 25, all 25 positive. Quality degradation: MNIST contrast reduction r = -0.573, p = 0.003, n = 25, 23/25 positive. The inverse magnitude pattern replicated across additive noise, impulse noise, and quality degradation, showing that the effect is not specific to one corruption type.

**Negative results:** Fashion-MNIST salt-pepper noise missed significance at p = 0.053 (diagonal range 0.270, below the 0.3 minimum threshold) and is excluded from claims. CIFAR-10 Gaussian had signal but insufficient diagonal range. The magnitude pattern does not apply to geometric transforms, applying it to rotation data would produce the wrong prediction direction.

**Prior art position:** The distinctive claim is that the same zero-shot confusion statistic can predict benefit magnitude in degradation scenarios, not just success versus failure. That is a different practical use case from ordinary transfer screening and is not described in prior art.

---

## Discovery 4: The Method Transfers Beyond Vision to Tabular Financial Data

**Finding:** Zero-shot diagonal strength correctly predicted negative transfer on 852,607 real Lending Club loans across a temporal regime shift from 2013-2014 to 2015-2016, showing the method is not limited to computer vision.

**Problem it solves:** A vision-only transfer predictor can be dismissed as image-specific. Cross-domain evidence is required to support a broader transfer learning prediction claim and to expand commercial applicability beyond computer vision.

**Why it matters:** Financial services temporal regime shifts are a common and costly problem in credit risk, fraud detection, and loan default prediction. A zero-shot predictor that can flag expected negative transfer before expensive fine-tuning has direct commercial value for banks, fintechs, and credit bureaus.

**Methodology:** Source model trained on Lending Club 2013-2014 (370,039 loans, 16.9% default rate) with class weighting (paid = 0.60, default = 2.96). Zero-shot evaluation on 2015-2016 data (852,607 loans, 17.7% default rate). Diagonal strength computed from zero-shot confusion matrix. Fine-tuned model trained on 682,085 target training loans for 20 epochs. Scratch model trained on same target data with 40 epochs (2x budget for fairness). Transfer advantage computed on 170,522 held-out test loans. Statistical significance tested via z-test on proportions. Full data integrity audit: 31/31 checks passed, 1,591 mathematical integrity checks passed.

**Evidence:** Zero-shot diagonal strength 0.6731. Prediction: negative transfer (diagonal below 0.7 threshold). Fine-tune accuracy 0.623. Scratch accuracy 0.645. Transfer advantage -0.0217 (-2.17%). Prediction correct. z = 13.24, p < 0.000001, n = 852,607 loans.

**Negative results:** This is a single test point in the financial domain, one temporal shift on one dataset. The per-architecture validation available for the image tests is not replicated here. The KMNIST to MNIST cross-domain test failed due to near-zero feature overlap, establishing that the method requires some domain similarity and does not generalize to completely unrelated domains.

**Prior art position:** Transfer learning prediction methods are predominantly validated on image benchmarks. Application to tabular financial data with temporal regime shifts is not described in prior transfer learning prediction literature. Use of confusion matrix diagonal strength as a cross-domain predictor without retraining or domain-specific feature engineering is a distinct contribution.

---

## Discovery 5: Off-Diagonal Entropy Is a Real but Secondary Signal

**Finding:** Off-diagonal entropy of the zero-shot confusion matrix is a statistically significant inverse predictor of transfer outcome, but only at sufficient sample size. At n = 12 the signal was not significant (r = -0.507, p = 0.092). At n = 96 the same signal reached significance (r = -0.313, p = 0.002). The finding establishes entropy as an independent secondary predictor alongside diagonal strength, and demonstrates that insufficient sample size can mask a real signal.

**Problem it solves:** Diagonal strength captures how often the model is correct. Off-diagonal entropy captures how structured or random the model's mistakes are across incorrect classes. These are structurally different quantities. No prior method used off-diagonal mistake randomness as a transfer predictor.

**Why it matters:** An independent secondary signal strengthens transfer prediction systems in regimes where diagonal strength has low variation across candidate models. The sample-size dependency is itself a finding: it establishes a lower bound on n needed to detect this signal reliably.

**Methodology:** Off-diagonal entropy computed from normalized off-diagonal mass of zero-shot confusion matrices across all 96 rotation tests (8 architectures by 12 angles). Pearson correlation computed between off-diagonal entropy and transfer advantage at both n = 12 and n = 96 to demonstrate the sample-size dependency directly.

**Evidence:** At n = 12: r = -0.507, p = 0.092 (not significant). At n = 96: r = -0.313, p = 0.002 (significant). Signal is weaker than diagonal strength (r = 0.445 at same n) but structurally independent. Direction: higher off-diagonal entropy predicts worse transfer outcome.

**Negative results:** The entropy signal is substantially weaker than diagonal strength and is best treated as a secondary or exploratory finding until it receives broader multi-domain validation. It requires n > 12 to reach significance, making it unreliable in low-sample settings.

**Prior art position:** Transfer learning predictors in prior art focus on diagonal performance metrics. Off-diagonal entropy as an independent predictor of transfer outcome with documented sample-size dependency is not described in prior art.

---

**Last Updated:** May 7, 2026
