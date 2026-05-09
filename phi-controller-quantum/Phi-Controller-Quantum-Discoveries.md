# Phi Controller Quantum — Scientific Discoveries

**Patent:** US Provisional Application No. 63/973,723
**Filed:** February 2, 2026
**Title:** Method and System for Real-Time Quantum Circuit Intervention Using Stability Metric Monitoring
**Repository:** https://github.com/Wise314/phi-controller-quantum
**Paper:** [10.5281/zenodo.20097808](https://doi.org/10.5281/zenodo.20097808)

---

## Scope

This repo extends the Phi portfolio from pre-execution selection (quantum-phi-validation, phi-hybrid-allocation) into during-execution closed-loop control. The provisional defines "real-time" to include discrete evaluation cycles over queued, pending, in-progress, segmented, checkpointed, or resumable execution states, which brings segmented and probe-based tests into scope without requiring continuous mid-circuit sensing. Evidence comes in three layers: simulated harness (4 scenarios), IBM calibration/telemetry harness (with fault injection), and 10 paid IBM hardware circuit tests across three backends (ibm_fez, ibm_marrakesh, ibm_torino).

---

## Discovery 1: Swapping LOW-Phi Qubits for HIGH-Phi Qubits on Real IBM Hardware Reduces Error by 85.1% Relative on the Same Circuit

**Finding:** On identical Deutsch-Jozsa circuits executed on the same IBM backend with the same shot budget, swapping the qubit pair from LOW-Phi to HIGH-Phi reduces error from 18.36% to 2.73%, an absolute reduction of 15.63 percentage points and a relative error reduction of 85.1%.

**Problem it solves:** Prior qubit-selection work relied on either experience-based heuristics or single calibration metrics, without a controlled same-circuit same-backend A/B demonstrating that a composite stability metric drives realized hardware error.

**Why it matters:** This is the clearest comparative link between the Phi score and realized sampling error. Without it, Phi is one more number on a calibration dashboard. With it, Phi becomes an actionable control variable for execution decisions.

**Methodology:** Test 1 on ibm_fez, January 15, 2026, executed Deutsch-Jozsa balanced on qubits [149, 150] with Phi values 0.097 and 0.030, 4096 shots, expected |01⟩. Test 2 on ibm_fez, January 17, 2026, executed the same circuit on controller-selected qubits [138, 151] with Phi values 0.9988 and 0.9984, 4096 shots. Only qubit selection differs between runs.

**Evidence:** Test 1 fidelity 81.64%, error 18.36% (3344 shots on |01⟩, 673 on |00⟩, 60 on |11⟩, 19 on |10⟩ out of 4096). Test 2 fidelity 97.27%, error 2.73% (3984 shots on |01⟩, 66 on |00⟩, 25 on |11⟩, 21 on |10⟩ out of 4096). Absolute reduction 15.63 percentage points. Relative error reduction 85.1%.

**Negative results:** None on this specific comparison. Honest counterexamples appear on other tests (see Discovery 8).

**Prior art position:** The cited prior work here does not establish a same-circuit same-backend A/B on current IBM hardware in which qubit pair swap alone produces this magnitude of error reduction using a composite stability metric.

---

## Discovery 2: Composite Phi Outperforms Raw T2 as a Qubit Selection Signal Under Matched Conditions

**Finding:** The composite stability metric Phi = I × rho - alpha × S produces lower error than selection by raw T2 alone when circuit, backend, day, and shot budget are held constant, with a 7.79 percentage point absolute improvement and 68.7% relative error reduction on the tested case.

**Problem it solves:** The most obvious critique of the Phi framework is "why not just pick qubits by T2?" The cited prior work here does not answer this question with a matched-condition hardware comparison.

**Why it matters:** If a single calibration field did the same work, the composite would be redundant. This result shows the composite adds realized value beyond the strongest single-field baseline.

**Methodology:** Test 8 on ibm_fez, January 29, 2026, same Deutsch-Jozsa balanced circuit, 4096 shots each, same day. Phi policy selected qubits [151, 152] on composite stability. RAW policy selected qubits [87, 88] by T2 alone (T2 scores 0.000206 and 0.000239).

**Evidence:** Phi policy error 3.54%, fidelity 96.46% (3951 shots on |01⟩ out of 4096). RAW policy error 11.33%, fidelity 88.67% (3632 shots on |01⟩ out of 4096). Absolute delta 7.79 percentage points. Relative error reduction 68.7%.

**Negative results:** This result holds on ibm_fez. Discovery 3 shows it does not hold on every backend.

**Prior art position:** Qubit selection literature typically uses single calibration fields or averages rather than component composites that encode identity, coherence, and entropy separately. This result shows the composite has measurable value over the strongest single-field baseline.

---

## Discovery 3: Phi-vs-T2 Advantage Generalizes Across Backends With Aggregate Net Gain But Not Uniform Dominance

**Finding:** Phi-based qubit selection outperforms T2-based selection on 2 of 3 IBM backends under matched conditions, with aggregate net gain of 6.37 percentage points across the three backends, while 1 backend shows the opposite result as an honest counterexample.

**Problem it solves:** Single-backend results risk reflecting device-specific artifacts. The cited prior work here does not test whether composite-Phi selection generalizes across heterogeneous IBM hardware in a controlled A/B.

**Why it matters:** Bounded generalization is more scientifically credible than universal-win claims. The result supports portability of the selection rule to new backends while identifying the boundary where the calibration proxy does not dominate realized error.

**Methodology:** Test 9, January 29, 2026, replicated the Test 8 protocol on ibm_fez, ibm_marrakesh, and ibm_torino. Deutsch-Jozsa balanced, 4096 shots per run, matched compiled depth (16) and two-qubit count (1) with zero swaps. Outcome was logged regardless of which policy won.

**Evidence:** ibm_fez, Phi error 3.49% vs RAW error 11.04%, delta +7.54pp, Phi wins. ibm_torino, Phi error 4.49% vs RAW error 7.79%, delta +3.30pp, Phi wins. ibm_marrakesh, Phi error 7.32% vs RAW error 2.86%, delta 4.47pp in favor of RAW, RAW wins despite higher Phi on RAW pair (0.9765 vs 0.9976). Phi wins 2 of 3 backends. Aggregate net advantage 6.37 percentage points in favor of Phi across the three backends.

**Negative results:** ibm_marrakesh is a documented proxy-disagreement interval. No bug, no calibration drift, verified by artifact inspection. Calibration-proxy Phi did not dominate realized error on that backend on that day.

**Prior art position:** The cited prior work here does not report matched-condition Phi-vs-T2 comparisons across multiple IBM backends with both wins and documented counterexamples.

---

## Discovery 4: Mid-Circuit Dynamic Branching on IBM Hardware Achieves 98.78% Marker-Verified Conditional Consistency

**Finding:** True mid-circuit measurement and classical conditional branching is executable on current IBM hardware at 98.78% consistency between probe outcome and branch marker, verified per-shot rather than inferred from the probe distribution alone.

**Problem it solves:** Prior "real-time" quantum control claims often reduced to telemetry refresh between jobs rather than in-circuit branching, and sentinel-circuit literature typically infers branch behavior from probe statistics without per-shot verification.

**Why it matters:** The controller's "during execution" claim is not a metaphor. Mid-circuit intervention is a working hardware capability on current IBM dynamic circuits, and the marker-bit design enables per-shot verification of which branch actually executed.

**Methodology:** Test 7 on ibm_fez, January 29, 2026. Compute qubits [0, 1], sentinel qubit [2]. Circuit of 12 cancel blocks with sentinel probe at block 6 and a compute-qubit marker bit set conditionally on probe outcome. 4096 shots. Expected compute result |01⟩.

**Evidence:** CONTINUE branch 2409 shots (58.8%), ABORT branch 1687 shots (41.2%). Joint marker/probe counts: 2385 shots probe=0/CONTINUE/marker=1, 1661 shots probe=1/ABORT/marker=0, 26 and 24 mismatched. Matched shots 4046 out of 4096. Conditional consistency 98.78%. Estimated 101,220 logical gates avoided on the ABORT path (6 remaining blocks times 10 gates per block times 1687 ABORT shots).

**Negative results:** Overall fidelity 25.27% by design, because the circuit intentionally used heavy cancel-work plus dynamic control plus noisy sentinel delay. This test proves branching correctness, not fidelity.

**Prior art position:** Distinct from quantum error correction, which uses syndrome measurements for correction rather than execution-management branching on a stability metric.

---

## Discovery 5: Wilson Confidence Interval Non-Overlap Enables Statistical Intervention Without Hardcoded Fidelity Thresholds

**Finding:** Intervention decisions can be driven by Wilson confidence interval non-overlap between probe estimates rather than by a fixed fidelity threshold, and 256-shot probes are sufficient to trigger decisive intervention when statistical separation exists.

**Problem it solves:** Threshold-based intervention rules (for example, "abort if fidelity below 90%") are brittle and ignore measurement uncertainty. The cited prior work here does not apply formal interval-based decision theory to quantum execution-management control.

**Why it matters:** This replaces arbitrary fidelity cutoffs with a principled statistical standard. The controller only intervenes when evidence of separation is strong enough to justify the cost of the intervention itself.

**Methodology:** Test 10, January 29, 2026, on ibm_fez, ibm_marrakesh, and ibm_torino. 256-shot probe on RAW pair, 256-shot probe on PHI pair, Wilson confidence intervals at alpha=0.05, continue-raw tie policy when intervals overlap. Deutsch-Jozsa balanced, expected |01⟩. Full 4096-shot run on chosen pair after decision.

**Evidence:** ibm_fez, RAW probe 84.77% CI (79.85%, 88.65%), PHI probe 95.70% CI (92.47%, 97.58%), non-overlap gap 3.82pp, action ABORT_PLANNED_RESTART, full PHI run achieved 95.85% fidelity. ibm_marrakesh, RAW 96.48% CI (93.45%, 98.14%), PHI 97.66% CI (94.98%, 98.92%), overlap -3.16pp, action CONTINUE, full RAW 97.46%. ibm_torino, RAW 93.36% CI (89.62%, 95.81%), PHI 98.05% CI (95.51%, 99.16%), overlap -0.30pp, action CONTINUE, full RAW 94.70%. Intervention triggered on 1 of 3 backends, which is the correct behavior when separation evidence is backend-dependent.

**Negative results:** 2 of 3 backends did not trigger intervention, correctly. The controller does not intervene without statistical evidence. Not all backends show exploitable separation on a given day.

**Prior art position:** The cited prior work here does not describe formal interval statistics for quantum intervention decisions.

---

## Discovery 6: Marginal-Phi Qubits Degrade on Deep Circuits Even When Calibration-Derived Phi Stays Flat Across Segments

**Finding:** Qubits with marginal but above-zero Phi values produce low fidelity on deep-circuit executions across multiple segments while the calibration-derived Phi signal itself stays flat, and depth-induced degradation of this kind was not detected by the periodic calibration snapshots used in this test.

**Problem it solves:** Pre-execution selection implicitly assumes that picking acceptable qubits once is sufficient. This result shows that even acceptable pre-execution selections can collapse on deep circuits in ways that only show up in realized sampling behavior.

**Why it matters:** This is the empirical motivation for during-execution monitoring rather than one-time pre-execution checks. If marginal Phi can become disastrous on deep executions while calibration snapshots stay flat, the controller needs to watch realized behavior in addition to re-polling telemetry.

**Methodology:** Test 3 on ibm_fez, January 29, 2026. Qubits [7, 17] with Phi values 0.3747 and 0.7574, minPhi 0.3747. Repeated Deutsch-Jozsa unit, 250 repeats, compiled depth 3751. Three segments of 2048 shots each with 40-second sleep between segments. Calibration snapshots taken between segments.

**Evidence:** Segment 1 fidelity 33.28%, error 66.72%. Segment 2 fidelity 30.44%, error 69.56%. Segment 3 fidelity 30.05%, error 69.95%. Average fidelity 31.26%, average error 68.74%. Phi drift across the three snapshots 0.0.

**Negative results:** The experiment does not isolate mid-execution Phi collapse from depth-induced degradation. Phi remained stable across the observation window, so the degradation is attributable to circuit depth on marginal qubits, not to measurable calibration change in this test. This bounds the claim to "realized depth degradation despite stable calibration snapshots" rather than "observed Phi collapse during execution."

**Prior art position:** Prior quantum characterization work focuses either on calibration drift over hours to days or on single-circuit fidelity curves. This result documents the specific regime of marginal-Phi plus deep-circuit plus flat-calibration that motivates execution-associated intervention.

---

## Discovery 7: Classical Checkpoint Representation Enables Suffix Migration to Alternative Qubits Without Storing an Unknown Quantum State

**Finding:** A classical representation of an observed measurement outcome at a segmentation boundary can serve as a checkpoint that enables the remaining computation to resume on alternative (HIGH-Phi) qubits, preserving computational progress without violating the no-cloning constraint on unknown quantum states.

**Problem it solves:** True quantum-state checkpointing would violate no-cloning. Prior work either ignored mid-execution recovery or relied on quantum error correction, which is not the same problem. The cited prior work here does not frame checkpointing as a classical-representation operation at a segmentation boundary for the purpose of qubit migration.

**Why it matters:** This is a no-cloning-compatible checkpoint primitive. It converts the theoretical no-migrate objection into an engineering question about where legitimate segmentation boundaries exist in a given workflow.

**Methodology:** Test 4 on ibm_fez, January 29, 2026. Prefix of 3 repeated Deutsch-Jozsa units on LOW-Phi pair [8, 9] with minPhi 0.0990. Segmentation boundary at classically-measurable outcome. Suffix of 22 repeated units on HIGH-Phi pair [151, 152] with minPhi 0.9984. 2048 shots each stage. Abort threshold 0.25. Controller decision: minPhi 0.0990 below threshold 0.25, action checkpoint_then_migrate_suffix.

**Evidence:** Prefix observed |01⟩ at 92.58% confidence. Suffix observed |01⟩ at 82.15% fidelity. Cost proxy delta (actual segmented cost vs counterfactual full restart) 92,160, computed as compiled depth times shots.

**Negative results:** Suffix fidelity 82.15% is lower than Test 2's 97.27% on a shorter circuit on the same HIGH-Phi pair, reflecting the realistic cost of segmentation plus resume. This bounds the claim to "preserves useful output" rather than "lossless."

**Prior art position:** The specification explicitly excludes storage of an unknown quantum state, which is what makes this claim compatible with no-cloning. Distinct from quantum error correction (syndrome-based correction of logical information) and quantum memory research (physical preservation of quantum state).

---

## Discovery 8: Calibration-Proxy Phi Does Not Dominate Realized Error in Every Execution Interval

**Finding:** Phi computed from periodic backend calibration is a proxy for realized sampling error, not ground truth. Intervals exist where a lower-Phi pair outperforms a higher-Phi pair under identical compiled circuit conditions, and these are not attributable to calibration drift or implementation error.

**Problem it solves:** Scientific credibility demands explicit boundary-setting. Claims of "Phi always wins" would be falsifiable on any sufficiently large test set and would not survive peer review.

**Why it matters:** The correct scientific statement is that Phi often improves selection decisions over the strongest single-field baseline, while counterexamples define the boundary of the calibration proxy. This framing supports downstream refinements such as execution-time sampling, randomized measurement estimates, or fused estimators.

**Methodology:** Two documented counterexamples in the paid hardware suite. Test 6 on ibm_fez, January 29, 2026, compared random pair [64, 65] with minPhi 0.7109 against Phi-guided pair [123, 124] with minPhi 0.9986 on repeated Deutsch-Jozsa units (25 repeats, compiled depth 376, 2048 shots). Test 9 ibm_marrakesh counterexample as described in Discovery 3.

**Evidence:** Test 6 random pair achieved 70.58% fidelity, outperforming Phi-guided pair at 64.31% fidelity despite lower Phi. Test 9 ibm_marrakesh RAW pair [53, 54] with Phi 0.9765 achieved 2.86% error, outperforming Phi-selected pair [58, 71] with Phi 0.9976 at 7.32% error, under matched depth 16, two-qubit count 1, and zero swaps. At least two documented counterexample intervals appear across the paid tests.

**Negative results:** This discovery is itself a negative-results finding about the boundary of the Phi proxy. It is included in the scientific record precisely because hiding it would weaken the overall claim set.

**Prior art position:** Calibration-proxy approaches in quantum computing rarely publish their counterexamples. Documenting the boundary is a methodological contribution distinct from the positive selection results.

---

## Discovery 9: Pre-Submit ABORT-RESTART Carries Zero Hardware-Execution Cost for the Aborted Attempt When Phi Is Extremely Low

**Finding:** When calibration-derived Phi is extremely low (including negative values), the controller can abort before job submission and restart on alternative qubits at zero hardware-execution cost for the aborted attempt, because the decision is made entirely from telemetry rather than from post-execution measurement.

**Problem it solves:** Prior reactive intervention strategies wait for partial or complete execution results before deciding to restart, which wastes hardware resources on doomed runs. The cited prior work here does not frame the aborted attempt as a zero-hardware-execution-cost operation distinguished from mid-execution abort.

**Why it matters:** The aborted attempt never incurs hardware execution cost. This distinguishes pre-submit ABORT from mid-execution ABORT and motivates front-loading the decision wherever telemetry supports it.

**Methodology:** Test 5 on ibm_fez, January 29, 2026. Candidate pair [72, 73] with minPhi -0.0487. Phi evaluation on calibration snapshot prior to API submission. Threshold 0.25 triggered pre-submit ABORT_RESTART. Restart pair [123, 124] with minPhi 0.9986 executed 25 repeated Deutsch-Jozsa units (compiled depth 376, 2048 shots).

**Evidence:** Pre-submit decision avoided 1 hardware job and 2048 shots on the low-Phi candidate pair. Restart execution achieved 70.29% fidelity (29.71% error).

**Negative results:** The experiment does not measure the realized fidelity that the avoided low-Phi pair would have produced, so the strongest claim is preventive resource saving rather than a measured completed-run delta. Near-zero fidelity on the avoided pair is expected but not measured here.

**Prior art position:** Distinct from reactive abort strategies that require partial execution to trigger.

---

## Discovery 10: Closed-Loop Phi Control Is the During-Execution Control Layer of the Quantum Phi Portfolio

**Finding:** The Phi framework, previously applied to pre-execution qubit selection (quantum-phi-validation) and pre-execution quantum-vs-classical routing (phi-hybrid-allocation), operates as a closed-loop control variable during execution-associated intervals, with intervention actions including CONTINUE, CONTINUE_DEGRADED, CLASSICAL_FALLBACK, CHECKPOINT_MIGRATE, and ABORT_RESTART selected by a priority-based decision hierarchy and bounded by confirmation-window, cooldown, and maximum-intervention guardrails.

**Problem it solves:** Prior quantum execution workflows were largely open-loop. Even where mid-circuit measurement or segmentation was available, no systematic decision framework linked a stability metric to a ranked action set under explicit guardrails.

**Why it matters:** This positions Phi as a control variable rather than a diagnostic score. The portfolio now spans the full execution lifecycle: which qubits to choose (quantum-phi-validation), quantum vs classical routing (phi-hybrid-allocation), and what to do when stability changes during the run (phi-controller-quantum). This repo adds the during-execution control layer.

**Methodology:** Three-layer validation. Simulated harness (January 11, 2026) exercised the four non-CONTINUE intervention actions at simulated Phi=0.19 across varying progress levels. IBM calibration harness on ibm_fez verified telemetry-based decision logic with fault injection (1 API call, 5 cached). Paid hardware tests (Tests 1 through 10, January 15 through 29, 2026) across ibm_fez, ibm_marrakesh, and ibm_torino validated CONTINUE, CHECKPOINT_MIGRATE, pre-submit ABORT_RESTART, mid-circuit branching, statistical intervention, and Phi-vs-T2 selection on real hardware.

**Evidence:** 4 simulated scenarios passed. IBM calibration harness passed including fault-injection trigger. 10 paid tests passed, using approximately 228 seconds of IBM credits. Five-action taxonomy instantiated across simulated, telemetry, and paid-hardware layers: CONTINUE (Test 2, 97.27% fidelity), CHECKPOINT_MIGRATE (Test 4, 82.15% final fidelity with 92,160 cost proxy delta), pre-submit ABORT_RESTART (Test 5, 2048 shots saved), mid-circuit branching (Test 7, 98.78% consistency), statistical intervention (Test 10, 3.82pp CI gap triggered ABORT on ibm_fez).

**Negative results:** CLASSICAL_FALLBACK and CONTINUE_DEGRADED have strong simulated and telemetry-layer validation but weaker paid-hardware evidence than the other three actions. The scientific record should keep these layered clearly rather than claiming all five actions have equivalent hardware validation.

**Prior art position:** Distinct from quantum error correction (syndrome-based correction of logical information) and error mitigation (post-processing bias reduction). The execution-management framing with a stability-metric-driven intervention hierarchy under explicit guardrails is not described in the cited prior work on quantum execution management.

---

## Cross-Repo Note: phi-controller-quantum Completes the Quantum Phi Execution Lifecycle

The Phi quantum portfolio now spans three execution stages.

quantum-phi-validation establishes Phi as a pre-execution qubit quality predictor: 445 qubits, r = 0.9458 correlation with T2/T1, 4.34x two-qubit gate error discrimination, 25-63x deep-circuit error discrimination, 83% error reduction from Phi-based qubit selection, 100% dead qubit detection.

phi-hybrid-allocation establishes Phi as a pre-execution routing variable: 23 tests across 7 algorithms on 3 IBM backends, error ratios from 2.90x (QPE) to 30.47x (Bernstein-Vazirani) under strict methodology, classical simulation outperforms LOW-Phi quantum execution in tested classically tractable circuits.

phi-controller-quantum adds the during-execution control layer: 10 paid hardware tests on 3 IBM backends, 85.1% relative error reduction from LOW-Phi to HIGH-Phi qubits on identical circuit, 68.7% relative error reduction over raw T2 selection under matched conditions, 98.78% per-shot mid-circuit branching consistency, Wilson CI statistical intervention triggered on 1 of 3 backends, classical checkpoint migration with 82.15% final fidelity.

Together the three repos cover the quantum execution lifecycle from pre-execution selection through pre-execution routing to during-execution control.

---

## Summary

| Discovery | Key Result |
|-----------|-----------|
| 1. LOW-Phi to HIGH-Phi swap | 18.36% to 2.73% error, 85.1% relative reduction |
| 2. Phi vs raw T2 | 7.79pp absolute, 68.7% relative reduction |
| 3. Cross-backend generalization | 2 of 3 backends, +6.37pp aggregate |
| 4. Mid-circuit branching | 98.78% per-shot conditional consistency |
| 5. Wilson CI intervention | 1 of 3 backends triggered, 3.82pp gap |
| 6. Marginal-Phi deep circuit | 31.26% fidelity at depth 3751, flat Phi |
| 7. Classical checkpoint migration | 82.15% suffix fidelity, no-cloning preserved |
| 8. Phi proxy boundary | 2 documented counterexamples, bounded claim |
| 9. Pre-submit ABORT-RESTART | 0 hardware cost on aborted attempt |
| 10. Closed-loop control layer | Five-action taxonomy, three-layer validation |

---

**Patent:** US Provisional Application No. 63/973,723
**Filed:** February 2, 2026
**Paper DOI:** [10.5281/zenodo.20097808](https://doi.org/10.5281/zenodo.20097808)
**Repository:** https://github.com/Wise314/phi-controller-quantum
