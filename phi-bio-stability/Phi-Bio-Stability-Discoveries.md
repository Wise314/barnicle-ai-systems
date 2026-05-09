# Phi Bio Stability — Scientific Discoveries

**Patent:** US Provisional Application No. 63/978,132
**Filed:** February 9, 2026
**Title:** Method and System for Real-Time Physiological Instability Detection Using Stability Metric Monitoring
**Repository:** https://github.com/Wise314/phi-bio-stability
**Paper:** [10.5281/zenodo.20098879](https://doi.org/10.5281/zenodo.20098879)

---

## Scope

This repo extends the Universal Phi framework from physical, quantum, neural-network, and LLM domains into biological signal monitoring. Evidence comes in two layers. The cardiac layer (Tests 1, 5, 6) is the strong primary contribution: AUC 0.9148 on MIT-BIH Arrhythmia, AUC 0.9015 in head-to-head HRV comparison, graceful resolution scaling from 60s to 5s windows. The neural layer (Tests 3, 3f, 3g) is exploratory: weak window-level AUC 0.51 to 0.61, K-of-N event recovery on a single patient, patient-specific direction. The methodology includes a documented self-falsification (the original chb01 single-hit event detection number was proven to be statistical chance via 3-consecutive audit). All evidence is from publicly available PhysioNet datasets with cardiologist or expert annotations. No synthetic data.

---

## Discovery 1: Phi Detects Cardiac Arrhythmia Transitions on MIT-BIH at AUC 0.9148 With Confirmed Real Signal

**Finding:** Phi = I × rho - alpha × S with alpha = 0.1, the same formula and coupling constant validated on bearings, turbofans, power grids, earthquakes, neural networks, and quantum qubits, also discriminates cardiac arrhythmia from normal sinus rhythm at AUC 0.9148 on the MIT-BIH Arrhythmia Database. The shuffle audit gap of 0.41 above chance (real AUC 0.9148 vs shuffle 0.5003) confirms the signal is not an artifact of label leakage or evaluation design.

**Problem it solves:** Whether the Phi structure and alpha = 0.1 coupling transferred to cardiac biological signals under physiological baseline calibration was not established by prior repos in this portfolio. Prior cross-domain validation covered engineered, computational, and geophysical systems, none of which involved physiological RR-interval data with patient-specific baseline variability.

**Why it matters:** This is the first biological-domain validation in the portfolio. Combined with prior validations, the framework now spans engineered, computational, geophysical, and biological systems under a single equation structure. The 14 of 48 records excluded for lacking baseline NORMAL windows establish a clear deployment boundary: per-record calibration requires identifiable baseline periods, which patients with continuous arrhythmias do not provide.

**Methodology:** 48 records from MIT-BIH Arrhythmia Database (PhysioNet, approximately 110,000 cardiologist-annotated beats from 1975 to 1979 at Beth Israel Hospital). 14 records excluded because no pure NORMAL windows existed for per-record threshold calibration, leaving 34 records and 1,022 evaluable 60-second windows. I = baseline_std_rr / current_std_rr clamped to 0 to 1. rho = lag-1 Pearson autocorrelation of RR intervals clamped to 0 to 1. S = Shannon entropy of RR distribution in bits with Freedman-Diaconis binning. Threshold calibrated per record at target_fpr = 0.01.

**Evidence:** AUC 0.9148, precision 91.71%, recall 72.02%, specificity 94.65%, F1 0.8068, MCC 0.6936. Confusion matrix: TP 332, FP 30, TN 531, FN 129 (n = 1,022 windows). Shuffle AUC 0.5003. Per-record baseline_std_rr varied substantially across records (example range from 0.0125 to 0.1513 across the 34 processed records), confirming per-record threshold calibration is doing real work rather than producing a uniform fit.

**Negative results:** 14 of 48 records (29%) excluded because they had no pure NORMAL windows for per-record calibration. Patients with continuous arrhythmias break the baseline-calibration assumption. The method as configured requires identifiable baseline normal periods to deploy at the per-subject calibration level used here.

---

## Discovery 2: Phi Detects Instability Transitions, Not Classification Between Stable Rhythm States

**Finding:** On the MIT-BIH Atrial Fibrillation Database (25 records, approximately 10 hours each, 11,988 evaluable windows), Phi achieves AUC 0.5556 distinguishing AFib from Normal sinus rhythm windows, essentially chance. A five-test audit confirms the negative result is real, not a bug or implementation error. The result supports the structural interpretation that AFib behaves here as a stable alternative rhythm state rather than a degradation transition, so the same Phi formula that detects transitions toward instability does not classify between two stable rhythm modes in this configuration.

**Problem it solves:** Without the AFib negative result, the cardiac arrhythmia AUC 0.9148 in Discovery 1 could be misread as a general claim that Phi classifies between any two cardiac states. The negative result establishes the boundary explicitly: in this configuration, Phi detects transitions, not stable alternative states.

**Why it matters:** This is a scope-defining finding. It strengthens Discovery 1 by ruling out the more general interpretation. It also provides interpretation for why Phi works on bearing, turbofan, earthquake, and quantum failure but not on classification between two stable operating modes. The AFib boundary makes the framework more falsifiable, which is itself a credibility property.

**Methodology:** 25 records processed (23 successfully, 2 with sampling errors). 11,988 evaluable 60-second windows under strict rhythm labeling policy (window must fall fully within one rhythm segment). Same Phi formula, same alpha = 0.1, same target_fpr = 0.01 as Discovery 1. Per-record threshold calibration. Five-test audit: shuffle test, global threshold, alpha sensitivity at 0.05 and 0.20, spot check.

**Evidence:** AUC 0.5556, precision 66.02%, recall 4.20%, specificity 98.89%, F1 0.0790, MCC 0.1006. Confusion matrix: TP 171, FP 88, TN 7,829, FN 3,900 (n = 11,988 windows). Shuffle audit AUC 0.4998. Alpha sensitivity at 0.05 produced AUC 0.5559, at 0.20 produced AUC 0.5545 (no improvement with alpha tuning). All five audit tests passed.

**Negative results:** This entire discovery is itself a negative result, presented as a scope boundary rather than a failure. The 4.20% recall does not invalidate the framework; it confirms the framework's intended scope in this configuration. Whether Phi can be adapted to detect onset-of-AFib transitions (rather than sustained AFib classification) is not established by this test.

---

## Discovery 3: Evidence Suggests Alpha May Be Modality- and Patient-Dependent in Physiological Signals

**Finding:** Cardiac validation supports alpha approximately 0.1 (consistent with physical, quantum, and neural-network domains in the portfolio). Exploratory EEG tests suggest that some neural configurations may require higher entropy weighting: alpha approximately 0.55 improved performance for chb01 and chb04 under the lower-Phi preictal direction relative to alpha = 0.1. This is not established as a universal EEG value because the result does not generalize to chb02 (opposite direction) or chb03 (weak in both directions).

**Problem it solves:** Prior repos in the portfolio used alpha = 0.1 with very minor exceptions. The neural EEG case is the first physiological domain where alpha = 0.1 produces materially worse results than a higher value on some patients. Without a modality-dependent framing of alpha, the framework would either need to drop the cross-domain alpha claim entirely or ignore the EEG calibration evidence.

**Why it matters:** The discovery refines rather than weakens the cross-domain claim. The structural form of Phi remains constant; the coupling constant may need to adapt to the role entropy plays in the monitored system. Cardiac systems are pumps where entropy is a minor disorder term; neural systems are information processors where entropy may carry more weight.

**Methodology:** Two independent grid searches. Cardiac: optimize_alpha_cardiac.py on MIT-BIH Arrhythmia data with the Test 1 windows. Neural: optimize_alpha_auc.py on CHB-MIT chb01 data with direction pre-specified as "lower Phi = preictal" before grid search. Direct comparison on chb04 with alpha = 0.10 and alpha = 0.55, all other parameters held constant.

**Evidence:** Cardiac alpha grid: alpha 0.05 AUC 0.9022, alpha 0.10 AUC 0.9022 (paper default, tied for best), alpha 0.15 AUC 0.9012, alpha 0.20 AUC 0.8995. Cardiac curve flat near optimum. EEG chb01 alpha grid: alpha 0.10 AUC 0.5409, alpha 0.20 AUC 0.5654, alpha 0.50 AUC 0.5807, alpha 0.55 AUC 0.5808 (best in this grid), alpha 0.60 AUC 0.5806. chb04 direct comparison: alpha 0.10 AUC 0.37, alpha 0.55 AUC 0.61, absolute gain 0.24 from alpha tuning alone on a held lower-direction patient.

**Negative results:** The neural alpha of 0.55 was determined on chb01 and validated on chb04, both of which exhibit "lower Phi = preictal" direction. The result does not generalize to chb02 (direction "higher Phi = preictal," AUC 0.31 at alpha 0.55) or chb03 (weak in both directions, AUC 0.51). The framing here is "alpha may be modality- and patient-dependent" rather than "alpha 0.55 is the universal neural value."

---

## Discovery 4: Consecutive-Hit Auditing Identified the Original 71% EEG Event Result as Chance-Inflated

**Finding:** The original Test 3 reported 71% seizure sensitivity (5 of 7 detected) on chb01 with window-level AUC 0.5654. Strict consecutive-hit auditing showed this single-hit result was not reliable. In Test 3d, the 3-consecutive requirement reduced detection to 0 of 7. In the later Test 3f comparison, the 3-consecutive comparator was 1 of 7 while K-of-N achieved 4 of 6 among seizures with sufficient coverage. Window-level AUC was unchanged at 0.5654 across both audit framings. Probability calculation explains the original number: with target_fpr = 0.01 per window and approximately 150 preictal windows per seizure, P(at least 1 hit by chance) = 1 - 0.99^150, approximately 78%. The original 71% sensitivity was within statistical noise for a low-AUC window-level signal multiplied across many independent windows.

**Problem it solves:** A weak window-level signal (AUC just above chance) with many independent windows per event will produce false-positive event-level detection that looks like real performance. Without an explicit null-hypothesis check, this artifact propagates as a methodological discovery and overclaims the result.

**Why it matters:** This is the methodological self-falsification that prevented the EEG result from being overclaimed. The audit also tells future readers what to look for in their own physiological-monitoring evaluations.

**Methodology:** Test 3d on chb01. Same windows, same Phi values, same threshold as the original Test 3. Event-detection rule changed from "at least 1 preictal hit anywhere in the preictal window" to "at least 3 consecutive preictal predictions." Test 3f re-ran the comparison with both 3-consecutive and K-of-N rules side by side. Probability calculation: 1 - (1 - target_fpr)^n_windows for n = 150, fpr = 0.01.

**Evidence:** Original Test 3 event sensitivity 71.43% (5 of 7), event-level FA/hr 3.78. Test 3d 3-consecutive event sensitivity 0 of 7 (0%), AUC unchanged at 0.5654. Test 3f 3-consecutive comparator 1 of 7 (14%), K-of-N 4 of 6 (67%) among seizures with K-of-N coverage. Theoretical chance rate at 1% per-window FPR with 150 windows is 78%, matching the original 71%.

**Negative results:** This discovery is itself the negative-result finding. The earlier 71% number appeared in the working repo and was retracted on the basis of this audit. The K-of-N rule (Discovery 5) is the corrected method.

---

## Discovery 5: K-of-N Event Detection Improves Single-Patient EEG Event Sensitivity Under Coverage Guard

**Finding:** Replacing the 3-consecutive event-detection rule with K-of-N (3 hits in any 10 consecutive windows) on chb01 raised seizure sensitivity from 14% (1 of 7 in Test 3f) to 67% (4 of 6 with K-of-N coverage) at event-level FA/hr 0.74. The mechanism is that preictal hits on EEG are scattered, not clustered: chb01_15 had a longest consecutive run of 2 hits (failing 3-consecutive) but multiple qualifying 3-of-10 blocks. The chb04 audit confirms K-of-N is recovering signal rather than relaxing the criterion, because 3-consecutive and K-of-N give identical results when the underlying signal is genuinely strong (1 of 3 detected by both rules). 67% sensitivity at 0.74 FA/hr is the strongest EEG event-level result in this repo, but remains single-patient and exploratory.

**Problem it solves:** EEG preictal signals have a temporally scattered structure that breaks consecutive-window event-detection rules. Without a method that tolerates scattered hits, weak but real preictal signals are missed entirely. The discovery follows directly from the falsification in Discovery 4 and provides the methodologically defensible replacement.

**Why it matters:** 67% sensitivity at 0.74 FA/hr is the first operationally plausible EEG result in the repo, but it remains single-patient exploratory evidence requiring multi-patient validation before any deployment claim can be made. The K-of-N rule is codified in the patent as a configurable event-level detection mechanism with refractory period and coverage guard.

**Methodology:** Test 3f on chb01 with alpha = 0.2, target_fpr = 0.05, refractory_period = 1,800 seconds (30 minutes), coverage requirement at least 10 preictal windows per seizure. K = 3, N = 10. chb01_21 excluded for insufficient coverage (3 preictal windows total). chb04 audit (Test 3g) tested whether K-of-N produces inflated detection on truly weak signals by comparing 3-consecutive and K-of-N on the same patient.

**Evidence:** K-of-N seizure sensitivity 67% (4 of 6 with coverage), 57% (4 of 7 all). Event-level FA/hr 0.74 (vs 3.78 original). Per-seizure breakdown: chb01_03 detected (24 hits in 150 windows), chb01_15 detected (33 hits, longest run 2), chb01_16 detected (13 hits in 71 windows), chb01_26 detected (21 hits in 150 windows). chb01_04 missed (7 hits in 117 windows, hit rate 6.0%), chb01_18 missed (7 hits in 142 windows, hit rate 4.9%). chb01_21 excluded (3 preictal windows total). chb04 audit: 3-consecutive and K-of-N both detect 1 of 3 seizures.

**Negative results:** Single-patient operational result on chb01. Window-level AUC unchanged at 0.5654; the K-of-N rule is a downstream event-detection improvement, not an underlying signal improvement. Two of seven seizures still missed despite coverage. Multi-patient K-of-N performance under Test 3g is patient-dependent: chb01 67%, chb02 0%, chb03 50%, chb04 33%. This is exploratory, not validated cross-patient seizure prediction.

---

## Discovery 6: Phi Trades Domain-Specific Performance for Cross-Domain Applicability at a Quantified 0.07 AUC Cost

**Finding:** On the same MIT-BIH cardiac arrhythmia detection task, Phi achieves AUC 0.9015 while the best cardiac-specific HRV metric (RMSSD) achieves AUC 0.9706, a gap of 0.0691. Phi ranks sixth among 16 evaluated metrics, ahead of SD1/SD2 (0.8930), HF Power (0.8537), and SD2 (0.8483). The Identity component I alone achieves AUC 0.9377, higher than full Phi, indicating the entropy term alpha × S costs Phi approximately 0.04 AUC on cardiac. This is the price paid for keeping the formula structurally identical to the form used in physical, quantum, and computational domains.

**Problem it solves:** A cross-domain framework can be dismissed as overclaiming if it does not honestly compare against domain-specific specialists. This finding quantifies the trade-off explicitly: Phi loses 0.07 AUC versus the best cardiac-specific HRV metric and gains cross-domain applicability across multiple validated domains in the portfolio.

**Why it matters:** The 0.07 AUC gap is the empirical cost of cross-domain applicability on this specific cardiac task. For any specific domain, hand-tuned domain methods may perform marginally better; Phi pays a small per-domain cost for not requiring per-domain method selection. The I-alone observation is a real internal finding worth keeping: on cardiac data, the full three-component formula performs slightly worse than the identity component alone, an honest within-formula cost. This must not be claimed as "Phi outperforms HRV"; the correct framing is competitive universality.

**Methodology:** 16 standard HRV metrics computed on the same 811 evaluable windows from MIT-BIH (after stricter inclusion of at least 5 normal windows per record): time-domain (SDNN, RMSSD, pNN50, SDSD, mean HR), frequency-domain (LF, HF, LF/HF), nonlinear (sample entropy, DFA alpha1, SD1, SD2, SD1/SD2). All metrics evaluated under identical calibration (per-record threshold, target_fpr 0.01). Shuffle sanity test confirmed no label leakage on Phi, RMSSD, or SDNN.

**Evidence:** Top 10 by AUC: RMSSD 0.9706, SDSD 0.9705, SD1 0.9705, I (Phi component alone) 0.9377, SDNN 0.9258, Phi 0.9015, SD1/SD2 0.8930, HF Power 0.8537, SD2 0.8483, rho (Phi component alone) 0.8477. Phi precision 87.14%, recall 69.32%, F1 0.7722, MCC 0.6887. Shuffle: Phi 0.5025 mean, RMSSD 0.5026 mean, SDNN 0.5009 mean, all near 0.50, no label leakage.

**Negative results:** Phi does not outperform domain-specific metrics on a domain-specific task. The framework's value is cross-domain operability, not best-in-class single-domain accuracy. The Identity component alone outperforms full Phi on this task (0.9377 vs 0.9015), indicating the entropy penalty alpha × S slightly reduces cardiac-specific accuracy in exchange for cross-domain consistency.

---

## Discovery 7: Phi Maintains Discriminatory Power Across Window Lengths From 60 Seconds to 5 Seconds With Graceful Degradation

**Finding:** On MIT-BIH Arrhythmia data, Phi achieves AUC 0.9148 at 60-second windows, 0.8775 at 30 seconds, 0.7642 at 10 seconds, and 0.6376 at 5 seconds. The degradation is smooth without a cliff. The 30-second result is within 0.04 AUC of clinical-resolution performance, and the 5-second result remains 0.14 above chance (0.50). Mechanism: smaller windows contain fewer heartbeats (approximately 3 to 6 at 5s vs approximately 30 at 30s), making entropy and coherence calculations statistically noisier.

**Problem it solves:** Clinical monitoring uses long windows (60 seconds and longer). Wearable and consumer monitoring requires short-window operation for low latency and limited battery. A method that only works at clinical resolution cannot deploy to wearables without recalibration or signal-processing redesign.

**Why it matters:** The graceful degradation profile shows that the same formula and alpha value retain signal across tested window lengths, while threshold calibration remains per-record. The same formula and alpha apply across clinical, bedside, wearable, and real-time-alert resolutions without changing the core Phi formula or alpha value, with min_rr scaled appropriately and per-record threshold calibration retained.

**Methodology:** Test 6 on the same MIT-BIH dataset and same parameters as Test 1, varying only window_sec across 5, 10, 30, 60. min_rr scaled with window length (3, 5, 10, 10 respectively). Same per-record threshold calibration at target_fpr = 0.01. Shuffle audit at 30s confirms signal is real at sub-clinical resolution.

**Evidence:** 60s: AUC 0.9148, recall 72.0%, precision 91.7%, F1 0.807, MCC 0.694, n = 1,022 windows. 30s: AUC 0.8775, recall 53.8%, precision 93.4%, F1 0.683, MCC 0.590, n = 2,285. Shuffle 0.5022, gap 0.38, signal real. 10s: AUC 0.7642, recall 32.1%, precision 92.2%, F1 0.476, MCC 0.451, n = 7,604. 5s: AUC 0.6376, recall 4.7%, precision 61.9%, F1 0.088, MCC 0.108, n = 15,975.

**Negative results:** Recall drops sharply at short windows (72.0% to 4.7%). The 5-second window has only 3 to 6 heartbeats per evaluation, denying the formula the statistical sample size that I, rho, and S require. Whether the 5-second resolution is operationally useful for wearable deployment depends on whether the user prioritizes specificity over sensitivity for the target use case.

---

## Discovery 8: Per-Subject Calibrated Thresholds Were Used When Entropy Was Unnormalized

**Finding:** In domains where entropy S is computed in absolute units (Shannon entropy in bits, range 5 to 6 bits for cardiac RR distributions) rather than normalized to 0 to 1, the fixed Phi_c = 0.25 threshold validated in earlier portfolio domains is not directly applicable. In this repo, per-record threshold calibration at a target false-positive rate (target_fpr = 0.01 quantile of Phi values from baseline windows) is used in place of fixed Phi_c = 0.25 because S is computed in bits rather than normalized to 0 to 1. The patent specification codifies both the fixed-threshold and the calibrated-threshold approaches as configurable embodiments.

**Problem it solves:** The Phi_c = 0.25 threshold validated in earlier portfolio domains assumed components normalized to 0 to 1. Applying that threshold to a formula where S is in bits would produce values that always classify as collapse. Without an alternative thresholding mechanism, the framework would not transfer to physiological domains where unnormalized entropy is the natural observable and patient variability is high.

**Why it matters:** This is a methodological extension that preserves the formula structure (Phi = I × rho - alpha × S) while adapting the threshold to unnormalized observables and per-subject deployment. Per-record calibrated thresholds derived from baseline windows function as the deployment-time analog of Phi_c, with the same operational interpretation (instability if Phi falls below threshold) but a numerical value derived from the data rather than fixed in advance.

**Methodology:** Per-record threshold calibration in the cardiac validation. For each record, all NORMAL windows produce Phi values; the threshold is the target_fpr = 0.01 quantile of those values. Records with insufficient NORMAL windows (14 of 48) are excluded from per-record calibration. The patent specification codifies this as a configurable embodiment with target FPR selectable per subject, per record, or per monitoring session.

**Evidence:** Per-record thresholds vary substantially across patients in the displayed examples. Record 100: threshold 0.076. Record 101: 0.004. Record 103: 0.116. Record 105: 0.271. Record 106: 0.129. The displayed example thresholds include values both below and near Phi_c = 0.25, showing that calibrated thresholds differ materially from a fixed threshold across the records examined. The same calibration approach applied to AFib produces AUC 0.5556, correctly identifying that Phi is not the right detector for that task. On cardiac, target_fpr = 0.01 calibration produces AUC 0.9148, recall 72.0%, precision 91.7%.

**Negative results:** Per-record calibration requires that each subject have a sufficient baseline period. Records without pure NORMAL windows (14 of 48 in MIT-BIH, 29%) cannot be calibrated under this approach. The threshold is no longer cross-record portable in absolute value, only in operational meaning (1% FPR per subject). A direct head-to-head test applying fixed Phi_c = 0.25 to the same MIT-BIH cardiac data was not run in this repo.

---

## Boundary and Negative Findings

The following findings are boundary conditions on the framework's neural application and are part of the scientific record.

EEG seizure prediction direction varies by patient. On CHB-MIT, chb01 and chb04 show "lower Phi = preictal" with AUC 0.58 and 0.61 respectively. chb02 shows the opposite "higher Phi = preictal" with AUC 0.70 in directional mode and 0.31 with chb01-direction Phi. chb03 shows neither direction reliably (AUC 0.51). Patient-specific direction calibration is required for EEG, consistent with how all clinical seizure prediction systems operate.

Absolute deviation mode (|Phi - baseline_Phi|) was tested as a direction-agnostic alternative for handling patient-specific direction variation. It did not solve the problem: chb01 AUC dropped from 0.57 to 0.50 (worse), chb02 from 0.70 to 0.72 (similar), chb03 from 0.54 to 0.51 (similar). Direction-free scoring does not recover the lost signal.

EEG observable mapping. The EEG observable mapping aggregates channels by per-channel demeaning and z-score normalization, then samplewise averaging across normalized channels. RMS amplitude and lag-1 autocorrelation are computed on the channel-aggregated signal. EEG S is spectral entropy in bits, computed from the squared magnitude of the discrete Fourier transform of the channel-aggregated signal in the current window with the DC bin removed and remaining bins normalized to a probability distribution. This is a structurally simple mapping (no per-channel features, no phase synchrony, no spectral band-specific power). Future work directions include spectral band power per channel, phase synchrony between channels, and patient-specific direction calibration.

---

## Summary

| Discovery | Key Result |
|-----------|-----------|
| 1. Cardiac arrhythmia detection | AUC 0.9148, shuffle 0.5003, gap 0.41 |
| 2. AFib boundary | AUC 0.5556, transition vs stable-state scope |
| 3. Modality-dependent alpha | Cardiac 0.1 stable, neural 0.55 patient-specific |
| 4. Self-falsification audit | 71% to 0 of 7 under 3-consecutive rule |
| 5. K-of-N event detection | 67% single-patient at 0.74 FA/hr |
| 6. Cross-domain trade-off | 0.07 AUC behind RMSSD, sixth of 16 metrics |
| 7. Resolution scaling | 0.9148 to 0.6376 from 60s to 5s windows |
| 8. Calibrated threshold | Per-record at target_fpr = 0.01 in bits-entropy |

---

**Patent:** US Provisional Application No. 63/978,132
**Filed:** February 9, 2026
**Paper DOI:** [10.5281/zenodo.20098879](https://doi.org/10.5281/zenodo.20098879)
**Repository:** https://github.com/Wise314/phi-bio-stability
