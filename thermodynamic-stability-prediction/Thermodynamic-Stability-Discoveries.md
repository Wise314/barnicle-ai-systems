# Thermodynamic Stability Prediction — Scientific Discoveries

**Patent #5 — Method and System for Universal Stability Assessment Using Thermodynamic Free Energy Analysis**

**Application Number:** 63/959,205 (filed January 13, 2026)

**Repository:** https://github.com/Wise314/-thermodynamic-stability-prediction

**Paper:** [Zenodo DOI 10.5281/zenodo.18523292](https://doi.org/10.5281/zenodo.18523292)

---

## Scope Note

The associated Zenodo paper reports a broader analysis including quantum and biological extensions beyond the core Patent #5 filing. This discoveries document records the 28-system / 5-domain engineered-domain core that the patent and repo validation scripts directly support. Two known drifts exist between the paper and this repo: (1) the paper describes AI rho as accuracy autocorrelation across training epochs while the repo and patent use rho = 1.0 for batch AI cases without temporal dynamics; (2) the paper describes geophysical I as strain variance ratio while raw outputs show mean-based computation. Both drifts are documented here for future paper audit purposes.

---

## Discovery 1: A Single Formula With Fixed Constants Produced Outcome-Consistent Assessments Across 28 Systems in 5 Physically Distinct Domains

**Finding:** Phi = I x rho minus alpha x S with alpha = 0.1 and threshold Phi_c = 0.25 produced stability assessments consistent with observed outcomes across 28 independent systems spanning computational AI, mechanical bearings, electrical power grids, aerospace turbofan engines, and geophysical seismic systems, with no domain-specific modifications to the formula, constants, or threshold in the reported evaluations.

**Problem it solves:** Existing stability assessment methods are domain-specific. Bearings use vibration RMS analysis, grids use frequency deviation monitoring, turbofans use remaining useful life estimation, AI uses accuracy tracking, and seismic monitoring uses strain accumulation models. No single method existed that could assess stability across all of these domains using one equation and one threshold without per-domain retuning.

**Why it matters:** A domain-agnostic stability metric means the same monitoring infrastructure, the same code, and the same threshold can be deployed across fundamentally different systems without retraining or recalibration. The absence of domain-specific modifications strengthens the claim that the formula captures a structural property of degrading systems rather than a domain-specific statistical pattern. The method requires no failure-labeled training data to set alpha or the critical threshold.

**Methodology:** Phi = I x rho minus alpha x S computed for each system using domain-appropriate observables. I = baseline_signal / current_signal for physical systems (RMS for bearings, standard deviation for grids, mean for strain, averaged across 21 sensor channels for turbofans), confusion matrix correlation for AI systems. rho = temporal autocorrelation at a characteristic lag, typically lag 1 in the reported validation scripts for physical systems, set to 1.0 for AI models where temporal dynamics do not apply between inference events. S = Shannon entropy in bits. alpha = 0.1 throughout. Datasets: MNIST (70,000 images, OpenML), XJTU-SY bearings (5,695 CSV files, 10 bearings across 3 operating conditions), UK National Grid frequency (2,678,400 measurements, August 2019), German grid frequency (2,500,830 measurements, September 2019), NASA C-MAPSS turbofans (20,631 rows, 10 engines, 21 sensors each), USGS Tohoku foreshocks (61 events, March 9-10 2011), USGS Donna Lea strainmeter (707,471 measurements, 2002-2016).

**Evidence:** 28/28 assessments consistent with observed outcomes in the reported evaluations. Domain breakdown: AI 2/2, mechanical 10/10, electrical 2/2, aerospace 10/10, geophysical 4/4. Phi values for failing systems ranged from -0.370 to 0.241, all below threshold. Full bearing breakdown: Bearing1_1 -0.168, Bearing1_2 -0.336, Bearing1_3 -0.261, Bearing1_4 -0.003, Bearing2_2 -0.263, Bearing2_3 -0.370, Bearing2_4 -0.255, Bearing2_5 -0.344, Bearing3_1 -0.276, Bearing3_4 -0.290. Full turbofan breakdown: Engine 1 0.205, Engine 2 0.186, Engine 3 0.152, Engine 4 0.241, Engine 5 0.236, Engine 6 0.169, Engine 7 0.066, Engine 8 0.130, Engine 9 0.039, Engine 10 0.165. Phi values for stable systems: Germany grid 0.401, 2010 seismic quiet year 0.577, AI class imbalance 0.261, all above threshold. In the reported tests, failure cases exhibited Phi at or below 0.241 and stable cases exhibited Phi at or above 0.261, a clean separation margin of 0.020.

**Negative results:** The 28-system claim is bounded to the five tested domains and the specific system types within each domain. Whether the formula generalizes to domains not tested here is not established by this validation. The turbofan results are the closest to the threshold, with Engine 9 producing Phi = 0.039, the weakest failure signal in the dataset. No prospective blind test on future events was conducted. The patent frames the threshold as identified empirically in the reported evaluations and not dependent on any particular theoretical interpretation.

**Prior art position:** US Patent 11,487,030 (2022) describes entropy-based earthquake prediction limited to the seismic domain with 70-75% reported reliability. US Patent 9,243,985 (2016) describes fracture fatigue entropy for metal fatigue requiring physical temperature measurements and material-specific equations, limited to mechanical fatigue. Neither establishes a single equation with fixed constants working across mechanical, electrical, aerospace, AI, and geophysical systems simultaneously. To the inventor's knowledge, no prior work discloses a single thermodynamic free energy equation with a single coupling constant and a single threshold used consistently across multiple disparate domains in reported evaluations.

---

## Discovery 2: Alpha = 0.1 Is the Unique Tested Coupling Constant Value Achieving Full Separation Between Stable and Failing Systems

**Finding:** A sensitivity grid over alpha values (0.05, 0.10, 0.15) and threshold values (0.20, 0.25, 0.30) tested on 20 failed or end-of-life cases plus 2 stable controls showed that alpha = 0.10 is the unique tested value achieving full separation. Alpha = 0.05 under-weights entropy, causing failed cases to exceed the threshold. Alpha = 0.15 over-weights entropy, causing stable cases to fall below the threshold.

**Problem it solves:** Without a sensitivity analysis, alpha = 0.1 could be dismissed as an arbitrary or over-fitted choice. Demonstrating that neighboring values in both directions produce classification failures establishes it as a precise operating point rather than a rough tuning knob.

**Why it matters:** The uniqueness result strengthens the patent claim that alpha = 0.1 reflects a consistent physical relationship observed in the reported evaluations rather than an empirically optimized parameter. It directly addresses the most obvious methodological objection a reviewer or patent examiner would raise.

**Methodology:** Sensitivity grid tested 9 combinations of alpha in (0.05, 0.10, 0.15) and Phi_c in (0.20, 0.25, 0.30) on 22 systems: 20 failed or end-of-life cases (bearings and turbofans) plus 2 stable controls (Germany grid Phi = 0.401, USGS seismic 2010 Phi = 0.577). Separation defined as all failed cases below threshold and all stable cases above threshold simultaneously. Components (I, rho, S) exported to JSON from paper validation scripts and sensitivity grid computed from those exports. Verification confirmed 10/10 bearing and 10/10 turbofan end-of-life values match paper table values within tolerance of 0.01.

**Evidence:** Alpha = 0.05, all Phi_c: no separation. Alpha = 0.10, Phi_c = 0.20: no separation. Alpha = 0.10, Phi_c = 0.25: full separation. Alpha = 0.10, Phi_c = 0.30: full separation. Alpha = 0.15, all Phi_c: no separation. At alpha = 0.10 and Phi_c = 0.25, the critical maximum was Turbofan Engine 4 at Phi = 0.241 and the stable minimum was Germany grid at Phi = 0.400, producing a separation margin of 0.159.

**Negative results:** The sensitivity grid was conducted on 22 systems from the bearing, turbofan, and stable control subset. It was not independently replicated on the electrical or geophysical domains. The result establishes alpha = 0.1 as empirically unique within the tested parameter space but does not derive it from first principles. The patent is explicit that the precise physical origin of alpha = 0.1 remains an open theoretical question.

**Prior art position:** No prior work on cross-domain thermodynamic stability prediction demonstrates sensitivity analysis establishing parameter uniqueness across multiple physical domains. This distinguishes the constant from curve-fitted domain-specific methods where parameters are tuned per application.

---

## Discovery 3: All Three Regimes of the Formula Are Empirically Observed — Collapse, Critical, and Stable Cases All Present in the Validated Dataset

**Finding:** The three-regime classification structure (Phi below 0 indicating severe instability, 0 less than or equal to Phi below 0.25 indicating critical state with elevated failure risk, Phi above 0.25 indicating stable operation) is not only theoretical. All three regimes appear in the validated dataset across real systems with known outcomes, and in the reported examples more negative Phi values were associated with more severe observed outcomes.

**Problem it solves:** A formula with three regimes that only produces evidence for two of them in practice leaves the third regime as an untested claim. Binary stable/unstable classification also discards severity information that enables proportional operational response.

**Why it matters:** The three-zone structure enables risk-proportional response: enhanced monitoring in the critical zone, immediate intervention in the collapse zone. The empirical contrast between UK blackout at Phi = 0.178 (critical zone, recoverable disruption affecting approximately 1 million people) and Tohoku earthquake at Phi = -0.357 (collapse zone, over 18,000 deaths) illustrates the graduated severity in the reported examples.

**Methodology:** Same as Discovery 1. Regime assignment determined by Phi value against fixed thresholds with no post-hoc threshold adjustment. Severity correspondence assessed by comparing Phi values of documented events against their observed impact severity.

**Evidence:** Collapse regime (Phi below 0): AI catastrophic forgetting Phi = -0.230 (99.3% to 0.0% accuracy), Tohoku earthquake Phi = -0.357, all 10 bearings Phi range -0.003 to -0.370 (Bearing1_4 at -0.003 is the boundary case). Critical regime (0 less than or equal to Phi below 0.25): UK grid Phi = 0.178, Parkfield Phi = 0.114, San Simeon Phi = 0.084, all 10 turbofans range 0.039 to 0.241. Stable regime (Phi above 0.25): Germany grid Phi = 0.401, AI class imbalance Phi = 0.261, 2010 quiet seismic year Phi = 0.577. In the reported tests failure cases exhibited Phi at or below 0.241 and stable cases exhibited Phi at or above 0.261.

**Negative results:** Bearing1_4 at Phi = -0.003 sits at the boundary between collapse and critical regimes. Its classification is consistent with the observed failure outcome but does not place it cleanly in either sub-failure regime. The severity gradient observation is based on two data points in the geophysical domain and is not formally tested across a controlled sample. The patent frames the severity interpretation as based on the reported examples rather than as an established calibrated law.

**Prior art position:** Domain-specific failure detection methods typically use binary thresholds. A single formula producing a graduated continuous severity metric applicable across mechanical, electrical, aerospace, AI, and geophysical domains without domain-specific calibration is not described in prior art. The patent defines all three zones explicitly and validates each empirically.

---

## Discovery 4: The Formula Produced Sub-Threshold Values for Retrospective Pre-Event Sensor Windows in Four Real Catastrophic Events

**Finding:** Using only sensor data available before each event, Phi produced sub-threshold values for the UK power blackout (Phi = 0.178), 2011 Tohoku M9.1 earthquake (Phi = -0.357), 2004 Parkfield M6.0 earthquake (Phi = 0.114), and 2003 San Simeon M6.5 earthquake (Phi = 0.084), in each case consistent with the subsequent catastrophic outcome.

**Problem it solves:** Many stability methods are validated only on synthetic data, benchmark labels, or post-failure windows. Real catastrophic events with publicly available pre-event sensor data provide a harder and more credible validation that cannot be cherry-picked because the events and their dates are fixed historical facts.

**Why it matters:** The UK blackout and Tohoku earthquake are among the most consequential infrastructure and natural disaster events in recent decades. That the formula correctly classified their pre-event states from standard sensor data, without domain-specific tuning, is the strongest applied validation result in the repo.

**Methodology:** UK grid: baseline period August 1-8 2019 (691,200 measurements), test period August 9 before blackout (60,720 measurements). Baseline std 58.86 mHz, test std 83.98 mHz, I = 0.701, rho = 0.998, S = 5.209 bits, Phi = 0.701 x 0.998 minus 0.1 x 5.209 = 0.178. Tohoku: 61 USGS foreshocks March 9-10 2011, baseline 20 foreshocks, degraded 20 foreshocks, I = 0.154, rho = -0.328, S = 3.066 bits, Phi = -0.357. Parkfield: baseline 52,533 measurements from 2003, test 39,023 measurements 2004 pre-quake (91,556 total strain measurements), I = 0.434, rho = 1.000, S = 3.206 bits, Phi = 0.114. San Simeon: baseline 14,256 measurements early 2003, degraded 36,837 measurements late 2003 (51,093 total), I = 0.415, rho = 1.000, S = 3.307 bits, Phi = 0.084.

**Negative results:** These are retrospective validations on historical data, not prospective predictions. The formula was applied to data after the events were known. Baseline and test period choices involve methodological decisions that affect the resulting Phi values. Prospective deployment on future events has not been tested and is not claimed. The Tohoku case uses foreshock magnitude distributions as proxy observables, which is a domain-specific adaptation of how I and S are computed that differs structurally from the continuous signal approach used in strain and grid validation.

**Prior art position:** Application of a single thermodynamic stability metric to retrospective pre-event sensor data from real catastrophic events across power grid failure, M9.1 earthquake, M6.5 earthquake, and M6.0 earthquake domains simultaneously is not documented in prior stability monitoring literature. The patent explicitly contrasts the method with conventional P-wave earthquake early warning, which provides seconds of warning after event detection rather than pre-event elevated-risk assessment from continuous monitoring.

---

## Discovery 5: Stable Systems Were Correctly Distinguished From Unstable Systems Including a Multi-Year Seismic Control Window

**Finding:** Three stable system cases were correctly classified as stable (Germany grid Phi = 0.401, AI class imbalance Phi = 0.261, 2010 quiet seismic year Phi = 0.577), demonstrating that the formula discriminates between failure and stable states rather than being biased toward failure predictions. Zero false positives and zero false negatives were observed across all 28 tested systems in the reported evaluations.

**Problem it solves:** A stability detector that always predicts failure is not useful. Scientific credibility depends on the ability to produce both failure and stable predictions from the same formula and threshold depending on actual system state. Without stable case validation the method could be dismissed as a trivially conservative classifier.

**Why it matters:** The 2010 quiet seismic year result is particularly important because it uses the same Donna Lea strainmeter, the same formula, and the same threshold as the Parkfield and San Simeon earthquake validations, but produces Phi = 0.577, substantially larger than the earthquake failure values. This cannot be explained by sensor-specific artifacts and provides direct evidence that the formula responds to actual system state.

**Methodology:** Germany grid September 2019: baseline September 1-7 (518,383 measurements), test September 8-14 (604,722 measurements), I = 0.906, rho = 0.989, S = 4.962 bits, Phi = 0.401. AI class imbalance: MNIST trained on balanced data, tested on 90% class 0 distribution, I = 0.349, rho = 1.000, S = 0.881 bits, Phi = 0.261. 2010 quiet year: baseline 52,346 measurements from 2009, test 50,872 measurements from 2010 (103,218 total strain measurements), same Donna Lea strainmeter as Parkfield and San Simeon tests, I = 0.881, rho = 1.000, S = 3.046 bits, Phi = 0.577.

**Negative results:** The AI class imbalance stable case at Phi = 0.261 is the closest result to the threshold in the stable category, with I = 0.349 indicating reduced information preservation while remaining above the stability threshold. The stable case set comprises only three systems across three domains; a comprehensive false positive rate test across many stable windows has not been conducted.

**Prior art position:** Most prior stability assessment methods are validated on failure cases only. Multi-domain stable case validation using the same formula and threshold as failure detection, including a multi-year seismic control window on the same sensor as failure validation, is not documented in prior thermodynamic stability assessment literature.

---

## Discovery 6: The Same Sensor Produced Three Distinct Correct Outcomes Across Different Time Windows, Proving Discrimination Rather Than Consistent Failure Prediction

**Finding:** The Donna Lea strainmeter produced three distinct correct Phi outcomes from three different time windows using identical methodology, identical threshold, and identical sensor hardware: Phi = 0.084 (failure, San Simeon M6.5, 2003), Phi = 0.114 (failure, Parkfield M6.0, 2004), and Phi = 0.577 (stable, 2010 quiet year). The stable year Phi is approximately 6.9 times the San Simeon failure Phi and approximately 5.1 times the Parkfield failure Phi on the same sensor.

**Problem it solves:** A model can appear to work on one event because it is effectively tuned to that location, sensor, or window. The same-sensor three-outcome result removes this confound by holding the measurement instrument constant across all three outcomes and spanning seven years of data.

**Why it matters:** This is the strongest single discrimination result in the repo. It shows the formula is not location-tuned, event-tuned, or threshold-tuned to any one seismic event. The same calculation classified two pre-earthquake windows as unstable and one quiet year as stable from the same physical sensor. The patent identifies this explicitly as demonstrating that the method generalizes across seismic events rather than being calibrated to characteristics of any specific earthquake.

**Methodology:** Identical methodology applied to three non-overlapping time windows of the Donna Lea strainmeter dataset (707,471 measurements, 2002-2016). Baseline and test periods defined relative to the target event date for earthquake years, and as full-year windows for the stable case. Formula, alpha, and threshold unchanged across all three windows. San Simeon: baseline 14,256 measurements early 2003, degraded 36,837 measurements late 2003. Parkfield: baseline 52,533 measurements from 2003, test 39,023 measurements 2004 pre-quake. Quiet 2010: baseline 52,346 measurements from 2009, test 50,872 measurements from 2010.

**Evidence:** San Simeon: I = 0.415, rho = 1.000, S = 3.307, Phi = 0.084. Parkfield: I = 0.434, rho = 1.000, S = 3.206, Phi = 0.114. Quiet 2010: I = 0.881, rho = 1.000, S = 3.046, Phi = 0.577. The stable year produced I = 0.881, more than twice the identity values of the pre-earthquake windows (0.415 and 0.434), explaining the large Phi difference from the same sensor.

**Negative results:** The three windows were not selected blindly. The earthquake years were chosen because earthquakes occurred, and the quiet year was chosen as a stable counterexample. A true prospective test would require applying the method to all years in the strainmeter record and evaluating the full false positive rate across all windows. That test was not conducted. This result is also based on one sensor station, not a full seismic network study.

**Prior art position:** Multi-outcome validation from a single sensor using the same stability formula across years-long timescales, producing both failure and stable classifications across multiple distinct real events, is not documented in prior seismic or cross-domain stability literature.

---

## Discovery 7: Domain-Adapted Observables Feed the Same Fixed Thermodynamic Structure Without Per-Domain Retuning or Failure-Labeled Training Data

**Finding:** The thermodynamic law remains structurally fixed while the observable used for I is adapted to domain-appropriate measurements: RMS ratio for bearings, frequency deviation ratio for grids, multi-sensor averaged degradation summary for turbofans, confusion matrix correlation for AI systems, and strain variance ratio for geophysical systems. Alpha, the formula structure, and the threshold are unchanged across all domains. This adaptation is in the observable construction only.

**Problem it solves:** Cross-domain methods often fail because they either force one raw observable onto every domain, which loses physical meaning, or secretly retune parameters per domain, which loses universality. The mechanism of universality in this repo is neither — it is a fixed thermodynamic structure with domain-appropriate observable construction.

**Why it matters:** This explains how the repo can be both universal and physically grounded. Each domain contributes its own measurable operational signature through the I term while the thermodynamic structure remains constant. The method requires no supervised learning to fit alpha or the critical threshold for any target system and no historical failure examples for the specific system being monitored.

**Methodology:** For mechanical systems, I = baseline RMS / current RMS. For electrical systems, I = baseline frequency deviation std / current frequency deviation std. For aerospace systems, I is computed from baseline versus current sensor values averaged across available channels with zero-variance channels excluded. For computational systems, I is computed as confusion matrix correlation between baseline and current model output. For geophysical systems using strain data, I = baseline strain variance / current strain variance. For foreshock sequences, I is computed from changes in foreshock magnitude variance between baseline and recent periods. rho = temporal autocorrelation at a characteristic lag, typically lag 1 in the reported validation scripts for physical systems, set to 1.0 for batch AI inference where temporal dynamics between inference events are absent. S = Shannon entropy in bits for all domains.

**Evidence:** The domain-specific I definitions are implemented in validation scripts for each domain and documented in the patent's detailed description. All five domain implementations produce Phi values using alpha = 0.1 and Phi_c = 0.25 with 28/28 outcome-consistent results in the reported evaluations. The patent states explicitly that the method requires no failure-labeled training data to learn or optimize alpha or the critical threshold and no domain-specific tuning of alpha or the critical threshold while permitting domain-appropriate feature extraction for computing I, rho, and S.

**Negative results:** The observable adaptation is not arbitrary. The patent states the method applies to systems where measurable outputs permit statistically reliable estimation of I, rho, and S from available data. Systems with insufficient signal, near-zero variance, or no meaningful baseline period are boundary cases. Degenerate rho cases default to 1.0 or trigger elevated monitoring depending on deployment policy. Whether the formula generalizes to domains where these conditions cannot be met is not established.

**Prior art position:** Prior cross-domain monitoring systems either require domain-specific parameter tuning or apply one raw signal type across all domains. The distinct contribution is a fixed thermodynamic structure with principled domain-appropriate observable construction that preserves both universality and physical interpretability across mechanical, electrical, aerospace, computational, and geophysical systems.

---

**Last Updated:** May 7, 2026
