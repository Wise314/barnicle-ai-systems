# Phi-Objective Controller Discoveries

**Patent #20:** Method and System for Stability-Guided Control Using Predictive Action Selection with Interchangeable Measurement Adapters
**Provisional Application No.:** 63/984,704 (filed February 17, 2026)
**Repo:** phi-objective-controller
**Paper:** Φ = I × ρ − α × S: A Domain-Agnostic Stability Metric and Autonomous Controller Validated Across Neural, Quantum, Mechanical, and Physiological Systems
**Zenodo DOI:** [10.5281/zenodo.18684052](https://doi.org/10.5281/zenodo.18684052)

---

## Scope Note

This repo extends the Phi framework from detection (monitoring Phi and reporting status) to closed-loop control (selecting interventions when Phi degrades). Evidence comes from three validated domains: Neural network training (SGD primary, Adam pilot transfer), Quantum circuits on real IBM hardware (three backends in March 2026, three backends in May 2026), and Mechanical bearings (XJTU-SY real data plus a physics-based simulator for surrogate training). The architecture separates a domain-agnostic controller engine (Core/objective_controller.py) from domain-specific adapters (phi_neural.py, phi_quantum.py, phi_bearings.py) and a per-domain surrogate model. Two architectural exceptions are documented honestly: (1) the Quantum runner does not route through the Core surrogate-loading path because Quantum's 12-feature vector differs from Neural's on slot 10 (log_gates_remaining vs log_lr) and the Core loader rejects models with mismatched feature names; (2) the Bearings surrogate uses 25 features (9 base plus 16 interaction) tuned to vibration-domain physics, not the standard 12-feature trajectory shared by Neural. The shared-engine claim therefore applies at the controller-logic level (action selection, four safety gates, audit structure), while the feature builder is per-domain. The Quantum surrogate exists as a pilot diagnostic (n=30, deployment_eligible=false) and is NOT wired into the runner; paid Quantum runs use heuristic action selection with predicted-circuit-success ranking from free IBM calibration data.

---

## Discovery 1: A Shared Phi-Control Architecture Separates Domain Adapters From Action-Selection Logic Across Neural, Quantum, and Mechanical Tests

**Finding:** The repo demonstrates a shared Phi-control architecture in which domain adapters compute I, rho, S, and Phi from native signals while a control engine applies four safety gates (late gate, budget cap, advantage gate, proxy-misalignment check) and returns domain-specific actions, defaulting to no_op if any gate fails. Neural and Bearings use the surrogate-guided controller path through Core/objective_controller.py directly. Quantum validates the same Phi-gating and action-selection concept through a separate runner that uses heuristic action selection with predicted-circuit-success ranking from free IBM calibration data.

**Problem it solves:** Prior monitoring tools are typically built inside the domain (a vibration threshold for bearings, an early-stopping rule for neural networks, a calibration heuristic for qubits) with incompatible metrics and decision logic across settings. The repo positions this as a shared adapter-engine-surrogate pattern that can be applied across domains by changing only the adapter and action set.

**Why it matters:** Deploying the controller in a new domain through the directly validated path requires two items: a domain adapter that maps native signals to (I, rho, S, Phi) and an action set appropriate to the domain. The Quantum domain validates the same Phi-gating concept through a separate runner that uses the same five Patent #17 actions (CONTINUE, CONTINUE_DEGRADED, CHECKPOINT_MIGRATE, CLASSICAL_FALLBACK, ABORT_RESTART) but with heuristic action selection rather than surrogate-guided selection.

**Methodology:** Three domain adapters (phi_neural.py, phi_quantum.py, phi_bearings.py) compute I, rho, S from domain-specific observables. Each adapter is paired with a domain action set (Neural: no_op, lr_down; Quantum: five Patent #17 actions; Bearings: CONTINUE, REDUCE_SPEED, LUBRICATE, MAINTENANCE). In the directly integrated path, the Core engine reads telemetry from adapters, runs the four safety gates, and returns an action. The Quantum runner runs a parallel heuristic action-selection path using the same Patent #17 action set and Phi gating logic, with migration target ranking computed from free IBM calibration data as the product over qubits of (1 minus 2Q_gate_error) times the product over qubits of (1 minus readout_error).

**Evidence:** Three domains validated. Neural SGD Run 16-L4: 14/20 wins at +0.084% mean delta, kill-only 20/20 perfect baseline match. Quantum March 1, 2026 milestone: 8 wins, 1 loss, 0 ties (88.9%) at +4.69% mean delta across three IBM backends. Bearings: 10/10 wins at +0.067 mean delta on the surrogate-guided controller A/B test across 10 seeds on the physics-based simulator.

**Negative results:** The shared-engine claim has two documented exceptions. The Quantum runner does not route through the Core surrogate-loading path because Quantum's 12-feature vector differs from Neural's on slot 10. The Bearings surrogate uses 25 features rather than the standard 12-feature trajectory shared by Neural. The architectural claim is therefore that the controller logic, the four safety gates, and the audit structure are domain-agnostic, while the feature builder is per-domain.

---

## Discovery 2: Direct Phi Maximization Produced a Proxy-Misalignment Counterexample, Leading to the Phi-as-Constraint Controller Design

**Finding:** Direct Phi maximization on the neural training task produced a documented counterexample where Phi rose while task accuracy fell, motivating the corrected controller design that treats Phi as a safety constraint and maximizes task performance. In Phase 1 Run 3 on CIFAR-10 ResNet-18 with 10% label noise, seed 2 reached mean Phi 0.294 with accuracy 75.8% under direct Phi maximization, while baseline training produced mean Phi 0.231 with accuracy 83.5% (higher Phi, lower accuracy). This counterexample established the design pivot to the corrected controller objective used in all subsequent phases.

**Problem it solves:** Without an explicit guardrail framing, a controller built on a stability metric is exposed to Goodhart-style proxy gaming. The repo positions this as a primary design decision: Phi is a constraint, not the optimization target.

**Why it matters:** This finding pivoted the controller objective from "maximize Phi" to "maximize expected task performance subject to Phi staying above a critical boundary." The corrected objective treats task performance as the optimization target and Phi as a penalty term that activates only when Phi crosses the critical boundary. This framing is what enables the Phase 2D, Phase 2E, Run 16-L4, and Adam transfer results to be net-positive without catastrophic regressions.

**Methodology:** Phase 1 testing on CIFAR-10 ResNet-18 with 10% label noise compared a Phi-maximizing policy against a baseline. The corrected controller objective penalizes predicted states where Phi falls below Phi_c minus a noise tolerance, while maximizing expected task performance over a planning horizon. Default parameters: horizon H=3, discount gamma=0.95, penalty M=10.0, Phi_c=0.25, tolerance tol=0.02.

**Evidence:** Phase 1 Run 3 produced the observed Phi-up performance-down case (Phi 0.294 vs 0.231; accuracy 75.8% vs 83.5% on seed 2). Subsequent phases used the corrected performance-first-with-Phi-guardrail objective. Phase 1 kill-only: 20/20 perfect baseline match. Phase 2D: 12/20 wins at +0.06% mean delta. Run 16-L4: 14/20 wins at +0.084% mean delta.

**Negative results:** This discovery is itself a negative-result finding documenting why direct Phi maximization fails. The Phase 1 Run 3 counterexample is a single-seed observation that motivated the design change; the supporting evidence for the corrected objective comes from the subsequent positive results across Phase 2D, Phase 2E, Run 16-L4, and Adam Transfer rather than from a separate ablation study isolating the constraint formulation.

---

## Discovery 3: Three Anti-Proxy Safeguards Were Implemented With a Conservative no_op Fallback in the Tested Neural Runs

**Finding:** Three guard layers (a performance floor, a step-wise rejection rule, and a history-based correlation monitor) operating in addition to the Phi-as-constraint formulation were implemented in the controller. When all candidate actions are rejected, the controller falls back to no_op. Across the reported neural active-control runs, worst losses were small: -0.27% in Phase 2D v1 and Phase 2E v2, -0.26% in the new-seed transfer test, -0.18% in Run 16-L4, -0.17% in cross-validation, and -0.02% in the Adam pilot. The largest single-seed gain reached +0.49%.

**Problem it solves:** The corrected Phi-as-constraint objective (Discovery 2) reduces but does not eliminate proxy-gaming risk. A controller without explicit per-action rejection logic could still execute actions where the constraint is satisfied but the task performance prediction is flawed.

**Why it matters:** The three-layer safeguard plus no_op fallback gives the controller a bounded downside even when the surrogate prediction is wrong. The risk profile observed in the neural runs is asymmetric: small bounded worst-case losses, larger potential gains. The largest gain (+0.49% on Seed 14 of Run 16-L4) is approximately 2.7 times the worst Run 16-L4 loss. The largest gain is approximately 2.9 times the worst cross-validation loss. In the Adam pilot, the largest gain (+0.23%) is approximately 11 times the worst loss (-0.02%).

**Methodology:** Three guard layers implemented in Core/objective_controller.py: performance floor requires candidate actions to satisfy perf_pred at least baseline minus epsilon over the planning horizon (default epsilon=0.02); step-wise rejection rejects any action predicted to increase Phi on the next step while decreasing performance; history-based correlation monitor tracks correlation between recent Phi and performance values, becoming conservative if correlation drops below a minimum threshold. If all candidates are rejected, the controller selects no_op.

**Evidence:** Run 16-L4 (20 seeds, SGD, L4 GPU): biggest win Seed 14 +0.49%, biggest loss Seed 11 -0.18%, mean +0.084%. Cross-validation (20 evaluation seeds, fully out-of-sample): biggest win +0.49%, biggest loss -0.17%, mean +0.073%. Adam Transfer (5 seeds, never seen by the surrogate): biggest win Seed 3 +0.23%, biggest loss Seed 4 -0.02%, mean +0.044%. Across all reported neural active-control phases, kill-only matched baseline 20/20 (SGD) and 5/5 (Adam) with zero false kills.

**Negative results:** The active controller does not win on every seed. Run 16-L4 produced 6/20 losses; Adam Transfer produced 3/5 losses; Phase 2D v1 produced 8/20 losses; new-seed transfer produced 9/20 losses with a near-neutral mean. The downside is bounded by the safety architecture but not eliminated. The controller is designed for a positive expected value with a small bounded worst case, not for guaranteed wins.

---

## Discovery 4: Hardware-Matched Surrogate Training Is Required for Positive Active Control on Neural Training, With In-Sample 70% and Out-of-Sample 60% Win Rates Confirming Generalization

**Finding:** A 12-feature trajectory surrogate trained and evaluated on the same hardware (L4 GPU) achieves 14/20 wins (70%) at +0.084% mean delta in-sample (Run 16-L4) and 12/20 wins (60%) at +0.073% mean delta on a fully out-of-sample two-fold cross-validation, both on CIFAR-10 ResNet-18 SGD training with 10% label noise across 20 deterministic seeds. Hardware matching is required: the same surrogate trained on T4 GPU and evaluated on L4 GPU produced 8/20 wins (40%) at -0.024% mean delta. Same code, same architecture, same safety gates; only the surrogate training data source changed.

**Problem it solves:** A surrogate that requires per-hardware training data is a deployment constraint, not a framework limitation. The finding documents the constraint explicitly so it can be addressed in deployment planning.

**Why it matters:** The 60% out-of-sample win rate (every evaluation seed unseen by the surrogate) confirms the in-sample Run 16-L4 result (70%) is not purely a fitting artifact. The 40% to 70% win-rate gap between hardware-mismatched and hardware-matched evaluation establishes that surrogate training data should be collected on the same hardware that will be used at deployment. The reward-to-risk ratio (biggest win divided by worst loss) is approximately 2.9 in cross-validation.

**Methodology:** 12-feature trajectory surrogate trained via per-action Ridge regression. Two-action set: lr_down (0.95x reduction factor) and no_op. Late-epoch gate active in last 10 of 30 epochs. Maximum 2 interventions per run. CIFAR-10 with 10% label noise. ResNet-18. Deterministic seeds 1-20 for in-sample (Run 16-L4) and cross-validation. All three surrogate quality gates required to pass before deployment: action differentiation, beats naive zero-change baseline, no degenerate per-action models. Cross-validation: Fold A trained on seeds 1-8, gate-checked on 9-10, evaluated on 11-20; Fold B trained on seeds 11-18, gate-checked on 19-20, evaluated on 1-10.

**Evidence:** Run 16-L4 (in-sample, L4 GPU): 14/20 wins (70%), 6/20 losses, mean delta +0.084%, biggest win Seed 14 +0.49%, biggest loss Seed 11 -0.18%, kill-only 20/20 perfect baseline match. Two-fold cross-validation (out-of-sample, L4 GPU): 12/20 wins (60%), 1/20 ties, 7/20 losses, mean delta +0.073%, biggest win +0.49% (Seed 14), biggest loss -0.17% (Seeds 4 and 6). T4-trained surrogate evaluated on L4 (cross-hardware untrained): 8/20 wins (40%), mean delta -0.024%.

**Negative results:** The cross-hardware untrained result (40%, -0.024%) is the documented honest baseline showing that hardware matching is required. The 70% to 60% drop from in-sample to out-of-sample confirms that the in-sample result is partially due to fitting on the specific seeds rather than full generalization.

---

## Discovery 5: Transfer to a New Seed Range and to a New Optimizer Define Coverage-Dependence Boundaries With Bounded Worst-Case Losses

**Finding:** Two transfer tests define coverage-dependence boundaries for the Neural domain controller. (1) Surrogate trained on Run 16-L4 seeds 1-20 evaluated on seeds 21-40 (never used anywhere in the project) produced 9/20 wins (45%), 2/20 ties, mean delta -0.019%, biggest win Seed 40 +0.25%, biggest loss Seed 23 -0.26%. (2) A 5-seed Adam optimizer transfer pilot using the same Phi formula, the same controller code, the same safety gates, and a separately trained Adam-specific surrogate produced 2/5 wins (40%), 3/5 losses, mean delta +0.044%, biggest win Seed 3 +0.23%, biggest loss Seed 4 -0.02%. Kill-only safety verification matched baseline 5/5 on Adam. Both transfer tests produced bounded worst-case losses without catastrophic failures.

**Problem it solves:** A controller that only works on its own training seeds and on its own optimizer has limited deployment value. The transfer tests make the coverage limits explicit rather than allowing the in-sample and cross-validation results to be misread as full generalization across all seeds and optimizers.

**Why it matters:** The new-seed-range transfer was near-neutral on average (-0.019%), motivating broader surrogate training data or nonlinear surrogate models. The Adam pilot demonstrated that the Phi formula and the surrogate pipeline (data collection, training, validation gates) generalize to a new optimizer at the level of a 5-seed pilot, with the largest gain (+0.23%) approximately 11 times the largest loss (-0.02%) within the pilot. Larger Adam runs are required for stronger statistical confidence.

**Methodology:** Seed-range transfer test: surrogate trained on Run 16-L4 with seeds 1-20 (12-feature trajectory, two-action set lr_down 0.95x and no_op), evaluation on seeds 21-40. Adam Transfer pilot: Adam optimizer at learning rate 0.001 on the same CIFAR-10 ResNet-18 with 10% label noise task. Adam-specific surrogate data collected on seeds 10-17 (192 transition rows). Surrogate trained on seeds 10-15, validated on holdout seeds 16-17, tested on seeds 1-5. All three Adam surrogate validation gates passed: action differentiation, beats naive baseline (39% better naive MSE), no degenerate models. Two-action set (lr_down 0.90x for Adam, no_op).

**Evidence:** New-seed transfer (seeds 21-40, L4 GPU): 9/20 wins (45%), 2/20 ties, 9/20 losses, mean delta -0.019%, biggest win Seed 40 +0.25%, biggest loss Seed 23 -0.26%, no catastrophic failures. Adam Transfer pilot per-seed: Seed 1 -0.01%, Seed 2 +0.04%, Seed 3 +0.23%, Seed 4 -0.02%, Seed 5 -0.02%. Adam mean +0.044%. Kill-only Adam 5/5 perfect match.

**Negative results:** Both transfer results show coverage dependence. The new-seed transfer test produced a near-neutral mean and 9/20 losses. The Adam pilot is small (n=5) with a 40% win rate and 3/5 losses, making the result initial transfer evidence rather than statistically settled validation. Larger-seed Adam runs are planned. The bounded worst-case losses confirm the safety architecture continues to work across both transfer settings.

---

## Discovery 6: A Heuristic Quantum Controller Using Free IBM Calibration Data Achieved 8/1/0 (88.9%) at +4.69% Mean Delta on the March 1, 2026 Hardware Milestone, With Backend-Dependent Behavior on the May 3, 2026 Expanded Run

**Finding:** A heuristic controller using free IBM calibration data (no machine learning, no surrogate, no GPU) achieved 8 wins, 1 loss, 0 ties at +4.69% mean delta across three IBM Quantum backends (ibm_fez, ibm_torino, ibm_marrakesh) on three deterministic seeds each on March 1, 2026. Phi was used as the gating signal; migration target ranking used predicted circuit success computed from calibration data as the product over qubits of (1 minus 2Q_gate_error) times the product over qubits of (1 minus readout_error). All win/loss pairs were hardware-scored with no heuristic scores in any win/loss claim. A 10-seed expanded paid run on May 3, 2026 on ibm_fez, ibm_marrakesh, and ibm_kingston produced an aggregate of 15W/15L/0T at +0.77% mean delta with strongly backend-dependent breakdown: ibm_fez 10/0 at +4.59% (matching March 1 behavior); ibm_marrakesh 5/5 at +0.21% (do-no-harm within noise); ibm_kingston 0/10 at -2.49% (systematic migration losses).

**Problem it solves:** Phi over-weights T2/T1 for shallow circuits (GHZ approximately 5 gates), so using Phi alone for migration target selection produces suboptimal qubit ranking. The predicted-circuit-success ranking provides a calibration-based target selection that is matched to the actual circuit being executed. Without explicit per-backend reporting, an aggregate near 50% would hide the operationally meaningful finding that one backend behaves opposite to the other two under the same controller logic.

**Why it matters:** This is the strongest applied quantum result in the repo. It uses only free IBM calibration data and physics-based qubit ranking. No training, no fitting, no labeled data, no GPU. On backends where the default qubits are already good (ibm_marrakesh predicted_success at least 0.92) the controller correctly does nothing (do-no-harm behavior). The May 3 kingston backend produced systematic migration losses, identifying the exact case where a trained surrogate could potentially correct the heuristic behavior. The kingston losses are bounded (worst -2.49% mean across 10 seeds) and backend-isolated.

**Methodology:** March 1 milestone: three IBM Quantum backends, three deterministic seeds per backend, GHZ 5-qubit circuit, SamplerV2 hardware execution with paid QPU time, conservative scoring (Bhattacharyya coefficient versus noiseless Aer simulation, taking the worse score between normal and bit-reversed output orders to prevent inflated wins), plan SHA-locked, surrogate explicitly disabled. May 3 expanded run: 10 deterministic seeds per backend on ibm_fez, ibm_marrakesh, and ibm_kingston (the latter as a previously-untested migration backend after ibm_torino was retired from open-instance access following March 1), same controller code, same Phi formula, same migration ranking, approximately 30 seconds of paid credits total.

**Evidence:** March 1 per-backend: ibm_fez 3/0 wins at +5.8% mean delta with all migrations to qubits [124, 123, 136, 143, 144] from default [0, 1, 2, 3, 4]; ibm_torino 3/0 wins at +8.2% mean delta with all migrations to qubits [9, 10, 11, 12, 18] from default [0, 1, 2, 3, 4]; ibm_marrakesh 2/1 (one loss within noise; mean +0.04%) with no migrations because predicted success on default qubits met the migration gate. Migration backends combined: 6/6 wins at +6.6% mean delta. May 3 per-backend: ibm_fez 10/0 wins at +4.59% (all CHECKPOINT_MIGRATE); ibm_marrakesh 5/5 split at +0.21% (all CONTINUE); ibm_kingston 0/10 wins at -2.49% (all CHECKPOINT_MIGRATE).

**Negative results:** The March 1 result is bounded to GHZ 5-qubit circuits on the three tested backends with three seeds each; Bernstein-Vazirani and Deutsch-Jozsa circuits were not tested in this milestone run. The May 3 kingston outcome is a real diagnostic finding: the calibration-based heuristic ranked migration as good but the actual migration outcomes were systematically negative on this backend. The closed-loop A/B paid test of surrogate vs heuristic remains the unlock that would validate whether a trained surrogate prevents the kingston losses.

---

## Discovery 7: Diagnostic Boundary Finding: The n=30 Quantum Surrogate Pilot Contains Learnable Per-Backend Signal but Is Not Runtime-Validated

**Finding:** A pilot Quantum surrogate trained on 30 sidecar training_eligible rows from the May 3, 2026 paid run achieves leave-one-seed-out mean absolute error 0.0092 versus naive-zero baseline 0.0261 (a 65% reduction), passes all three deployment quality gates (action differentiation with spread 0.0252, beats both naive zero and naive action-mean baselines, no degenerate per-action models), and correctly predicts the sign of per-backend deltas. The pilot is explicitly marked deployment_eligible=false and pilot_diagnostic=true. Runtime integration is not implemented: the runner uses heuristic action selection regardless of which surrogate JSON files exist on disk.

**Problem it solves:** The kingston losses (Discovery 6) are the case a trained surrogate could potentially correct. The pilot establishes that per-backend learnable signal exists in the 12-feature vector at n=30, while keeping the deployment status honest (n=30 is below the spec minimum of 200 rows).

**Why it matters:** The pilot is evidence that the feature vector contains learnable signal, not deployment validation. Three prerequisites separate the pilot from a deployment-ready quantum surrogate: a quantum-aware runtime loader that can handle the 12-feature quantum vector with slot 10 as log_gates_remaining; sufficient training data (the spec calls for 200 rows minimum, the pilot has 30); and a closed-loop A/B paid test of surrogate vs heuristic to validate prevention of the kingston losses.

**Methodology:** Sidecar-driven, per-action standardized Ridge with leave-one-seed-out cross-validation, three quality gates. Training rows: 20 CHECKPOINT_MIGRATE plus 10 CONTINUE. Three quality gates: Gate 1 action differentiation (spread 0.0252), Gate 2 beats both naive baselines, Gate 3 no degenerate per-action models.

**Evidence:** LOSO MAE 0.0092 vs naive-zero MAE 0.0261 (65% reduction). All three gates passed. Per-backend LOSO predictions within 0.002 of true means: ibm_fez true +0.0459 predicted +0.0443; ibm_kingston true -0.0249 predicted -0.0229; ibm_marrakesh true +0.0021 predicted +0.0021. Coefficient finding: the fitted CHECKPOINT_MIGRATE Ridge model is dominated by phi_t and phi_margin (both +0.0174 standardized; perfectly correlated because phi_margin equals phi_t minus 0.25); the other 10 features are near-zero by construction because perf_history is held flat between checkpoints in adapter mode. CONTINUE uses mean fallback because there is no fitted Ridge for that action class.

**Negative results:** n=30 is well below the spec minimum of 200 rows. Single circuit type (GHZ) only; Bernstein-Vazirani and Deutsch-Jozsa untested. CHECKPOINT_MIGRATE is the only action with a real fitted Ridge model; CONTINUE uses mean fallback; three other Patent #17 actions (CONTINUE_DEGRADED, CLASSICAL_FALLBACK, ABORT_RESTART) have zero training rows. Pilot model is deployment_eligible=false. Runtime integration is not implemented.

---

## Discovery 8: The Same Phi Formula Detected 13/15 XJTU Bearings (87%) on Real Run-to-Failure Data and a Surrogate-Guided Controller Won 10/10 on the Physics-Based Simulator With Survival Extended From 43-45 to 51-60 Steps

**Finding:** Two layers, kept separate.

**Layer 1 (real-data detection, no controller):** The same Phi formula with alpha = 0.1 and Phi_c = 0.25 used across the portfolio detected 13 of 15 XJTU-SY bearings (87%) at end-of-life, while the bearing-specific adaptive-threshold formula from the system-degradation-framework repo detected 10 of 15 (67%) on the same dataset. The fixed-form Phi formula caught 4 bearings the domain-specific formula missed (Bearing2_1, Bearing3_2, Bearing3_3, Bearing3_5) and missed 1 the domain-specific formula caught (Bearing1_3, due to a leakage-guard config constraint, not a Phi failure). The two missed bearings are honestly attributable to data limitations.

**Layer 2 (simulator-based active control):** A surrogate-guided controller using Ridge regression with 25 features (9 base plus 16 interaction features) won 10 of 10 A/B test seeds against a do-nothing baseline at +0.067 mean delta on the physics-based bearing defect simulator (PierrickRauby/Bearing_defect_simulation, MIT license). Baseline survival was 43 to 45 simulator steps; surrogate-guided survival was 51 to 60 steps. In the simulator, interventions are growth-coupled to defect dynamics, so the measured survival extension is not merely a waveform-measurement artifact.

**Problem it solves:** A cross-domain framework can be dismissed as overclaiming if it does not honestly compare against domain-specific specialists on the same dataset under matched conditions. The XJTU-SY dataset is run-to-failure with no interventions, so controller intervention claims must be validated on a separate simulator that supports growth-coupled physics; the real-data and simulator results must be kept distinct.

**Why it matters:** Layer 1 is the strongest cross-domain detection result in the repo: the same equation that monitors neural network training and quantum decoherence outperforms a formula-specific competitor on its own dataset, while preserving honest documentation of the two missed bearings. Layer 2 demonstrates that the same surrogate architecture pattern used in the Neural domain (per-action Ridge regression with quality gates) generalized to bearings with 10/10 wins on the simulator. The 25-feature vector for Bearings is documented as a per-domain feature builder exception to the 12-feature Neural standard.

**Methodology:** Layer 1 (real data): XJTU-SY Bearing Degradation Dataset (15 bearings across 3 operating conditions: 35 Hz/12 kN, 37.5 Hz/11 kN, 40 Hz/10 kN). Bearings adapter computes I = baseline_rms divided by current_rms (clamped to 0 to 1); rho = lag-1 autocorrelation of RMS over a sliding window; S = Shannon entropy of FFT frequency spectrum (normalized to 0 to 1). alpha = 0.1, Phi_c = 0.25. Leakage guard enforced (late_gate at least baseline_files). 3-consecutive-alert streak required for detection. CPU only (MacBook), under 10 minutes total runtime for all 15 bearings. Layer 2 (simulator): physics-based bearing defect simulator. Training data: 36 episodes times 60 steps across 6 policies with 6 seeds each, total 2,066 (state, action, outcome) rows. Patent-grade alignment: actions chosen from PRE-action observed state; outcomes are POST-action next state. Surrogate: Ridge regression with interaction features. Train/test split by episode (not by row) to prevent time-correlation leakage. Episode-split test R-squared 0.20 (the honest metric); train R-squared 0.84. A/B test: 10 seeds, 43 to 60 steps each, late gate plus budget cap plus advantage gate.

**Evidence:** Layer 1 detection: 13 bearings detected. Lead times range from 1 step (Bearing1_4) to 2,296 steps (Bearing3_1). 2 bearings missed (Bearing1_3, Bearing1_5) for documented data-limitation reasons. Domain-specific formula on the same data: 10 of 15 (67%). Phi caught and domain-specific missed: Bearing2_1, Bearing3_2, Bearing3_3, Bearing3_5. Phi missed and domain-specific caught: Bearing1_3. Layer 2 A/B test: 10 of 10 wins. Per-seed delta(mean Phi) ranges from +0.0104 (Seed 42) to +0.1124 (Seed 11). Mean delta +0.067. Baseline survival 43 to 45 steps; surrogate survival 51 to 60 steps. Action distribution: REDUCE_SPEED 64 times, LUBRICATE 18 times, MAINTENANCE 0 times.

**Negative results:** Bearing1_3 misses because baseline_files = 92 and failure_point = 93, leaving 1 step of runway after the leakage guard. Bearing1_5 misses because the dataset contains only 52 files and the failure point is at step 101. Both misses are config or data limitations, not Phi failures. Episode-split test R-squared is 0.20; row-split R-squared is 0.84 but inflated by time-correlation leakage within episodes; the episode-split metric is the honest one. MAINTENANCE never fires in the controller's selected actions across the 10 A/B test seeds. The simulator-based intervention result does not validate real-bearing intervention effects; the XJTU-SY dataset is run-to-failure with no interventions and cannot be used to validate the controller's action selection on real hardware.

---

## Summary

| Discovery | Topic | Result Type |
|-----------|-------|------------|
| 1 | Shared Phi-control architecture across Neural, Quantum, Mechanical | Hardened positive (with documented exceptions) |
| 2 | Direct Phi maximization counterexample motivates Phi-as-constraint | Negative-result-driven design pivot |
| 3 | Three anti-proxy safeguards with bounded worst-case losses | Hardened positive |
| 4 | Hardware-matched surrogate 70% in-sample, 60% out-of-sample on Neural | Hardened positive |
| 5 | Seed-range and Adam optimizer transfer with bounded losses | Coverage-boundary finding |
| 6 | Heuristic Quantum controller 88.9% on March 1, backend-dependent on May 3 | Hardened positive (with kingston diagnostic) |
| 7 | n=30 Quantum surrogate pilot has learnable signal but not runtime-validated | Diagnostic boundary finding |
| 8 | XJTU 13/15 detection plus simulator 10/10 surrogate-guided wins | Hardened positive (two layers) |

---

**Inventor:** Shawn Barnicle
**Email:** ShawnBarnicle.ai@gmail.com
**GitHub:** https://github.com/Wise314
**Patent Status:** Provisional - Pending. © 2025-2026 Shawn Barnicle. All Rights Reserved.
