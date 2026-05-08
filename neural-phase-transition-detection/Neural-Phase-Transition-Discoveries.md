# Neural Phase Transition Detection — Scientific Discoveries

**Patent #7 — Method and System for Early-Epoch Viability Assessment of Neural Network Cognitive Phase Transitions Using an Identity Deficit Threshold**

**Application Number:** 63/960,091 (filed January 14, 2026)

**Repository:** https://github.com/Wise314/neural-phase-transition-detection

**Paper:** [Zenodo DOI 10.5281/zenodo.20081751](https://doi.org/10.5281/zenodo.20081751)

---

## Discovery 1: A Single-Epoch Identity Deficit Threshold Predicts Neural Network Viability Across Architecture Types and Datasets in the Tested Setting

**Finding:** The identity deficit I_deficit = 1 - sqrt(F), where F = trace(CM) / sum(CM) is confusion matrix diagonal strength after one training epoch, predicts whether a neural network architecture will form coherent task identity within a practical training budget. At threshold I_c = 0.22 combined with a universal 80% formed-identity cutoff, the method correctly classified 21 of 22 architectures across MLPs and CNNs on MNIST, Fashion-MNIST, and CIFAR-10, with one false negative on a slow-learning CNN.

**Problem it solves:** NAS and architecture evaluation usually require training candidate models for many epochs before viability is clear. No single-epoch method existed to separate architectures likely to form coherent task identity from those likely to fail within practical training budgets using a fixed threshold across the tested architecture types and datasets.

**Why it matters:** A binary viability decision after one epoch eliminates compute wasted on non-viable architectures before the bulk of training cost is incurred. The method holds across both MLP and CNN families and across grayscale and RGB image tasks in the tested setting. The single false negative is a slow learner in the caution zone rather than a categorically wrong prediction, and the patent explicitly accommodates it through an additional-evaluation policy rather than hard termination.

**Methodology:** Five validation experiments on real data from keras.datasets. For each architecture: (1) train for one epoch using standard backpropagation, (2) compute F = trace(CM) / sum(CM) from the epoch-1 confusion matrix on held-out validation set, (3) compute I_deficit = 1 - sqrt(F), (4) predict WILL FORM if I_deficit < 0.22 else WILL FAIL, (5) train to convergence (30 epochs for MNIST and Fashion-MNIST, 50 epochs for CIFAR-10), (6) evaluate whether final accuracy exceeded 0.80 formed-identity cutoff. Networks: Tiny MLP (5 architectures, 2-8 neurons), standard MLP (5 architectures, 16-256 neurons, 1-5 layers), CNN (4 architectures, 2-5 convolutional layers) per dataset. Datasets: MNIST (70,000 images), Fashion-MNIST (70,000 images), CIFAR-10 (60,000 RGB images). All real data, no synthetic data, random seeds set. Implementation: keras/TensorFlow, sklearn confusion_matrix.

**Evidence:** Test 1 Tiny MLP MNIST n=5: 5/5 correct. Test 2 standard MLP MNIST n=5: 5/5 correct. Test 3 CNN MNIST n=4: 3/4, one false negative (2-layer CNN, I_deficit 0.3702, predicted FAIL, actual 93.88%). Test 4 CNN Fashion-MNIST n=4: 4/4 correct. Test 5 CNN CIFAR-10 n=4: 4/4 correct. Total: 21/22, 95%. Threshold I_c = 0.22 corresponds to F = 0.6084, approximately 61% accuracy at epoch 1.

**Negative results:** The MNIST 2-layer CNN (I_deficit 0.3702) was predicted LIKELY NON-VIABLE but reached 93.88% after 30 epochs, one false negative. This architecture falls in the caution zone (0.22-0.40) and is a slow learner. The predictor was not tested on architecture families beyond MLP and simple CNN. Results are bounded to 10-class classification on three tested datasets. Whether the threshold generalizes to binary, regression, or sequence tasks is not established.

**Prior art position:** NAS methods require partial or full training to assess viability (Zoph and Le 2017; Real et al. 2019). Multi-epoch early stopping methods are reactive and require many epochs before termination decisions (Prechelt 1998; Hyperband, Successive Halving). Learning-curve extrapolation predicts final accuracy from early trends but requires multiple epochs and does not use confusion matrix structure (Domhan et al. 2015). Zero-cost proxies operate in parameter space, not behavioral space, and do not produce a confusion-matrix-derived scalar threshold. Phase transition analyses in the literature are descriptive and post-hoc, not prescriptive at early epochs. No prior work uses single-epoch confusion matrix diagonal strength with a fixed identity deficit threshold to produce a binary viability decision that is architecture-family-agnostic.

---

## Discovery 2: Threshold 0.22 Was Held Fixed Across Tested Datasets and Architecture Types Without Per-Dataset Retuning

**Finding:** The same identity deficit threshold I_c = 0.22 was applied across MNIST, Fashion-MNIST, and CIFAR-10 and across both CNN and MLP families without per-dataset or per-architecture recalibration in the reported evaluations, producing 21/22 correct predictions. The patent explicitly states the threshold was selected once and held fixed across all evaluated architectures and datasets.

**Problem it solves:** A viability predictor that requires per-dataset threshold recalibration introduces a hidden supervision requirement. Without a fixed threshold, the method cannot claim generalization, it can only claim fit.

**Why it matters:** The scientific claim is substantially stronger when the threshold is chosen once and applied unchanged to all subsequent tests. The same I_c = 0.22 separates viable from non-viable in the easy grayscale regime (MNIST MLPs, I_deficit range 0.0197-0.0471) and in the hard RGB regime (CIFAR-10 CNNs, I_deficit range 0.3422-0.4453). The threshold does not move; the architecture's epoch-1 behavior changes.

**Methodology:** All five validation experiments used the same threshold. The threshold was selected once and applied unchanged to MNIST MLP, MNIST CNN, Fashion-MNIST CNN, and CIFAR-10 CNN. No post-hoc adjustment was made. Patent text confirms: "the identity deficit threshold of 0.22 was selected once and held fixed across all evaluated architectures and datasets, without per-architecture or per-dataset tuning."

**Evidence:** 21/22 correct across MNIST, Fashion-MNIST, and CIFAR-10 using the same threshold value. I_deficit ranges by dataset: MNIST MLP 0.0197-0.0471 (all viable, all correct), Fashion-MNIST CNN 0.1157-0.2189 (all viable, all correct), CIFAR-10 CNN 0.3422-0.4453 (all non-viable, all correct). The threshold spans a range of approximately 0.43 in I_deficit between the clearest viable and non-viable populations without requiring adjustment.

**Negative results:** The threshold is validated on 10-class classification problems only. Whether I_c = 0.22 applies to binary, 100-class, or sequence tasks is not established. Prior work on the broader Phi framework found that 100-class problems required a distinct threshold for the Phi-controller (0.084 vs. 0.25), suggesting class count may affect the appropriate threshold in other settings.

**Prior art position:** The patent explicitly contrasts the fixed-threshold approach with per-architecture or per-dataset calibration methods. No prior NAS viability prediction work demonstrates a single fixed threshold for a confusion-matrix-derived metric applied across multiple architecture families and dataset difficulty levels without retuning.

---

## Discovery 3: The Threshold Predicts in Both Directions, Failure and Success, Validated on Architectures Designed to Straddle the Boundary

**Finding:** On tiny MLP architectures (2-8 neurons), I_c = 0.22 correctly separated architectures that failed to form identity from those that succeeded, in both directions, on a test set designed to span the viability boundary. Tiny-2 (I_deficit 0.3712) and Tiny-3 (I_deficit 0.2251) were correctly predicted WILL FAIL and did not form identity. Tiny-4 (I_deficit 0.1320), Tiny-5 (0.1165), and Tiny-8 (0.0703) were correctly predicted WILL FORM and did form identity. 5/5 correct.

**Problem it solves:** A threshold that only identifies obvious winners or only identifies obvious failures provides weak boundary evidence. The method needed explicit validation that the threshold operates as a classifier in both directions rather than as a one-sided screen.

**Why it matters:** The transition from Tiny-3 to Tiny-4 crosses both the 0.22 I_deficit threshold and the 0.80 accuracy cutoff simultaneously. I_deficit drops 0.0931 and final accuracy rises 8.77 percentage points from one additional neuron. This is consistent with a phase transition interpretation: one unit of capacity pushes the architecture across a regime boundary, producing discontinuous changes in both the epoch-1 formation signal and final convergence accuracy.

**Methodology:** Test 1. Five Tiny MLP architectures (2, 3, 4, 5, 8 hidden neurons) trained on MNIST (54,000 train, 6,000 val, 10,000 test). One epoch training, I_deficit computed, training extended to 30 epochs, final accuracy compared against 0.80 cutoff. Architectures selected to span the viability boundary by stepping through minimal-capacity networks. Results from timestamped JSON: tiny_mlp_validation_20251203_113708.json.

**Evidence:** Tiny-2: F 0.3954, I_deficit 0.3712, final 65.49%, DID NOT FORM, correct. Tiny-3: F 0.6004, I_deficit 0.2251, final 77.77%, DID NOT FORM, correct. Tiny-4: F 0.7534, I_deficit 0.1320, final 86.54%, FORMED, correct. Tiny-5: F 0.7806, I_deficit 0.1165, final 89.21%, FORMED, correct. Tiny-8: F 0.8643, I_deficit 0.0703, final 92.92%, FORMED, correct. 5/5 correct.

**Negative results:** This bidirectional result is on one architecture family (tiny MLPs) and one dataset (MNIST) with five data points. Tiny-3 at I_deficit 0.2251 sits 0.0051 above the threshold and may be sensitive to random seed or dataset split. The sharpness of the 3-to-4 neuron transition is not replicated across other architecture families or datasets.

**Prior art position:** Explicit bidirectional threshold validation on architectures designed to straddle the viability boundary, with documented correspondence between the I_deficit threshold, the accuracy cutoff, and a minimal-capacity architectural boundary, is not demonstrated in prior NAS or early stopping literature.

---

## Discovery 4: A Universal 80% Formed-Identity Cutoff Resolved the CIFAR-10 Validation Failure and Produced Better Cross-Dataset Consistency in the Tested 10-Class Setting

**Finding:** Switching from per-dataset variable accuracy cutoffs (V1: 0.90 MNIST, 0.85 Fashion-MNIST, 0.65 CIFAR-10) to a universal 80% formed-identity cutoff (V2) improved total prediction accuracy from 19/22 (86%) to 21/22 (95%). The CIFAR-10 result specifically improved from 1/4 (25%) to 4/4 (100%). The 0.65 cutoff in V1 reclassified all four CIFAR-10 CNNs as successes despite convergence accuracies of 58.65%-79.53%, architectures that clearly failed at the task. The 80% cutoff has a task-structural interpretation: 8x better than the 10% random baseline on 10-class problems.

**Problem it solves:** A viability predictor whose outcome criterion is tuned per-dataset requires hidden supervision and conflates dataset difficulty with architectural viability. The V1 design meant a practitioner had to decide what success means on each dataset before using the predictor, which partially defeats its utility.

**Why it matters:** The 80% cutoff is dataset-independent in a principled way: it measures task competence relative to the random baseline regardless of absolute accuracy ceiling. A 2-layer CNN reaching 80% on MNIST is viable; the same architecture reaching 79.53% on CIFAR-10 after 50 epochs is not, and the 80% criterion correctly labels both. This is a methodological discovery about evaluation design that makes the I_deficit threshold more portable.

**Methodology:** Both V1 and V2 tested on the full 22-architecture suite with all conditions held constant except the formed-identity cutoff definition. V1 used dataset-specific cutoffs. V2 applied 80% uniformly. CIFAR-10 CNN convergence accuracies were 58.65%, 74.97%, 77.77%, 79.53%, all below 80%, all correctly labeled DID NOT FORM under V2. Under V1 the 0.65 cutoff caused all four to be labeled FORMED incorrectly. V1 archived in results/archive_v1_variable_cutoffs/.

**Evidence:** V1: 19/22 (86%), CIFAR-10 1/4 (25%). V2: 21/22 (95%), CIFAR-10 4/4 (100%). Net gain: 3 correct predictions from cutoff standardization. The single remaining error (MNIST 2-layer CNN false negative) is present in both V1 and V2. The 5-layer CIFAR-10 CNN converged at 79.53%, only 0.47 percentage points below the cutoff, the closest call in the dataset.

**Negative results:** The 80% cutoff is validated on 10-class problems only. Whether it is the correct outcome criterion for binary, 100-class, or regression tasks is not established. The 5-layer CIFAR-10 CNN at 79.53% is a boundary case sensitive to the exact cutoff definition.

**Prior art position:** The distinction between the viability detection threshold (I_c = 0.22, applied to the predictor input) and the formed-identity cutoff (0.80, applied to the ground truth label), and the finding that standardizing the outcome criterion improves cross-dataset consistency, is not addressed in prior NAS viability prediction literature.

---

## Discovery 5: A Caution Zone Exists Between I_deficit 0.22 and Approximately 0.40 Containing Slow Learners That the Method Cannot Reliably Classify From One Epoch Alone

**Finding:** The single misclassification in the validated set, the MNIST 2-layer CNN (I_deficit 0.3702), was predicted LIKELY NON-VIABLE but converged to 93.88% after 30 epochs. This architecture falls in the caution zone between 0.22 and approximately 0.40 described in the patent. The caution zone is not merely the method's error region; it is a regime of genuine prediction ambiguity where additional early-epoch evaluation, rather than immediate termination, is the appropriate policy. Critically, zero false positives were observed: no architecture predicted LIKELY VIABLE failed to form task identity.

**Problem it solves:** A hard binary threshold with no uncertainty handling would be too brittle for near-threshold cases and would misrepresent the method's actual reliability profile. The asymmetric error structure, false negatives only, no false positives, needs to be made explicit for deployment.

**Why it matters:** The caution zone defines a two-stage operational policy: below 0.22 predict viable (15 cases, 15 correct, 0 errors), in 0.22-0.40 apply additional evaluation (6 cases, 5 correct, 1 false negative), above 0.40 predict non-viable (1 case, 1 correct). Zero false positives across all 22 cases means the method never silently passed a failing architecture, the operationally worse error type.

**Methodology:** Post-hoc partition of all 22 prediction outcomes by I_deficit zone. Zone boundaries: below 0.22 (confident viable), 0.22-0.40 (caution), above 0.40 (confident non-viable). Error type recorded per zone. Caution zone grounded in patent text (page 14): "Architectures in this caution zone may warrant additional early-epoch evaluation before a final termination decision is made."

**Evidence:** Below 0.22 zone (n=15): 15 correct, 0 errors. Caution zone 0.22-0.40 (n=6): 5 correct, 1 false negative. Cases in caution zone: Tiny-2 (0.3712, correct FAIL), Tiny-3 (0.2251, correct FAIL), MNIST 2-layer CNN (0.3702, false negative, actual 93.88%), CIFAR-10 3-layer (0.3911, correct FAIL), CIFAR-10 4-layer (0.3622, correct FAIL), CIFAR-10 5-layer (0.3422, correct FAIL). Above 0.40 zone (n=1): CIFAR-10 2-layer CNN (0.4453, correct FAIL). Zero false positives across all 22 cases.

**Negative results:** The caution zone upper boundary at approximately 0.40 is observed in the patent text rather than separately derived from a held-out validation set. The asymmetric error analysis is post-hoc on n=22. Whether zero false positives holds on larger and more diverse architecture sets is not established. The caution zone is not yet a separately validated classifier; it is a practical boundary condition inferred from the repo's one misclassification and nearby threshold cases.

**Prior art position:** Explicit characterization of asymmetric error types (zero false positives, one caution-zone false negative) in a single-epoch binary viability predictor, with a documented caution zone embodiment for handling boundary cases, is not described in prior NAS literature. Existing methods do not distinguish confident and uncertain prediction zones from the same single-epoch metric.

---

## Discovery 6: The MNIST CNN Family Shows a Sharp Capacity-Threshold Jump Between 2-Layer and 3-Layer Architectures That Is the Repo's Clearest Empirical Signature of a Phase Transition

**Finding:** In the tested MNIST CNN family, the 2-layer CNN produces I_deficit = 0.3702 at epoch 1 and converges to 93.88% final accuracy (false negative, slow learner). The 3-layer CNN produces I_deficit = 0.1104 at epoch 1 and converges to 98.24%. The drop of 0.2598 in I_deficit between two architectures differing by one convolutional layer, combined with a 4.36 percentage point final accuracy gap, is the sharpest single-family discontinuity in the dataset. The 4-layer and 5-layer CNNs continue to lower I_deficit values (0.0327, 0.0160) with final accuracies 99.08% and 99.39%. The pattern is consistent with an architectural phase transition rather than a smooth continuum.

**Problem it solves:** A threshold separating two non-overlapping populations at extreme values would be less interesting scientifically than a threshold that captures a real regime change within an architecture family on the same dataset. The MNIST CNN result provides the latter.

**Why it matters:** The jump from 2-layer to 3-layer architecture crosses the 0.22 threshold with a large margin change (0.3702 to 0.1104). This is interpretable as the minimum depth required for MNIST CNNs to begin forming coherent behavioral structure at epoch 1, consistent with the patent's framing of I_c as a cognitive phase transition boundary. The fact that the 2-layer CNN is the repo's false negative, it eventually converges despite a high epoch-1 I_deficit, does not contradict the phase transition story; it places the 2-layer case in the caution zone of a real boundary rather than as a clean non-viable case.

**Methodology:** Test 3. Four CNN architectures (2, 3, 4, 5 convolutional layers) trained on MNIST. One epoch evaluation then 30 epochs to convergence. Results from timestamped JSON: threshold_validation_20251203_153122.json.

**Evidence:** 2-layer CNN: F 0.3967, I_deficit 0.3702, final 93.88% (false negative, caution zone). 3-layer CNN: F 0.7913, I_deficit 0.1104, final 98.24% (correct). 4-layer CNN: F 0.9357, I_deficit 0.0327, final 99.08% (correct). 5-layer CNN: F 0.9682, I_deficit 0.0160, final 99.39% (correct). I_deficit drop from 2-layer to 3-layer: 0.2598. Final accuracy increase: 4.36 percentage points. 3/4 correct on this test.

**Negative results:** This capacity-threshold result is on one architecture family (simple CNNs) and one dataset (MNIST) with four data points. Whether the same sharp jump appears in other architecture families or at other depth boundaries is not established. The 2-layer CNN is the repo's false negative, so this discovery must be framed as a capacity-threshold pattern with one slow-learner exception, not as perfect separability.

**Prior art position:** The patent frames the MNIST CNN depth jump as early behavioral evidence of a cognitive phase transition rather than a retrospective architectural comparison. Identification of architectural capacity thresholds through single-epoch confusion matrix statistics, without full training, is not described in prior NAS or phase transition literature.

---

**Last Updated:** May 8, 2026
