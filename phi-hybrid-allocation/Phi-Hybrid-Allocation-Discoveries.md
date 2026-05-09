# Phi Hybrid Allocation — Scientific Discoveries

**Patent:** US Provisional Application No. 63/956,752
**Filed:** January 9, 2026
**Title:** Method and System for Stability-Driven Quantum-Classical Hybrid Resource Allocation
**Repository:** https://github.com/Wise314/phi-hybrid-allocation
**Paper:** [10.5281/zenodo.20094713](https://doi.org/10.5281/zenodo.20094713)

---

## Scope

This repo validates a routing decision framework that uses real-time Phi-based qubit quality to allocate computation between quantum hardware and classical simulation. It contains 23 validated tests across 7 algorithms on 3 IBM backends: 16 research-grade tests and 7 patent-grade strict tests. The strict tests (Tests 17-23, January 5-8, 2026) are the primary scientific evidence layer. They require verified physical qubit binding via q.index, decisive ground truth checks with abort on failure, connected-only LOW-Phi qubit selection preferring negative-Phi values, and abort on transpiler remap. The research tests are supporting context only. All results are from real IBM Quantum hardware execution. No synthetic data. Note that one strict test, Simon's Algorithm (Test 21), used a fallback LOW selection because no negative-Phi connected quad was available on the tested backends, making it the weakest of the strict tests.

---

## Discovery 1: A Real Quantum-Classical Crossover Exists Where Classical Simulation Outperforms LOW-Phi Quantum Execution in the Tested Setting

**Finding:** In the tested crossover experiments on classically tractable circuits on IBM Quantum hardware, exact classical simulation at 0% error outperformed LOW-Phi quantum execution. The primary crossover test shows classical 0.00% error, HIGH-Phi quantum 2.87% error, and LOW-Phi quantum 8.23% error on a GHZ circuit. The strict Bernstein-Vazirani result extends this: classical 0.00%, HIGH-Phi 2.12%, LOW-Phi 64.72%, a 30.47x error ratio. The same crossover pattern held across QFT, Grover, Deutsch-Jozsa, and GHZ under both research and strict methodology.

**Problem it solves:** Quantum execution is often treated as inherently preferable to classical simulation for quantum algorithms. This result shows that on LOW-Phi qubits, that assumption fails in the tested setting. Without a routing decision, circuits are blindly assigned to hardware regardless of qubit quality, producing unreliable results.

**Why it matters:** This is the core new scientific contribution of Patent #15 relative to the earlier quantum Phi repos. Patent #9 showed which qubits are bad. Patent #15 adds the stronger result that bad qubits can make quantum execution worse than doing nothing and that classical fallback is the correct engineering response.

**Methodology:** 23 real IBM hardware tests across ibm_fez, ibm_torino, and ibm_marrakesh. Primary crossover test used a 3-qubit GHZ circuit with HIGH-Phi qubits from ibm_fez (min-Phi 0.999) and LOW-Phi qubits from ibm_marrakesh (min-Phi 0.052). Strict Bernstein-Vazirani used qubits with Phi values -0.026 and -0.020 for LOW selection. Phi = I x rho - 0.1 x S with threshold 0.25. All real hardware execution, no synthetic data.

**Evidence:** Primary crossover: classical 0.00%, HIGH-Phi 2.87%, LOW-Phi 8.23%. Strict BV: classical 0.00%, HIGH-Phi 2.12%, LOW-Phi 64.72%, ratio 30.47x. Pattern confirmed across QFT (classical 2.10% vs LOW-Phi 16.65%), Grover (classical 0.00% vs LOW-Phi 13.75%), and Deutsch-Jozsa (classical 0.00% vs LOW-Phi 7.54%).

**Negative results:** The crossover claim is bounded to classically tractable circuits where exact or near-exact classical simulation is available at low cost. The repo does not claim all quantum workloads should be classically simulated. It claims that LOW-Phi execution can be worse than classical fallback in the tested setting. One above-threshold case at Phi = 0.730 still produced 39.97% error in the threshold sweep, showing that 0.25 is a useful routing boundary but not a guarantee of low error.

**Prior art position:** The cited prior work does not describe a validated quantum-vs-classical execution crossover based on a training-free scalar stability metric applied in real-time before circuit execution on IBM Quantum hardware.

---

## Discovery 2: The Routing Decision Is Governed by the Weakest Qubit in the Circuit, With Min-Phi Across Circuit Qubits as the Operative Routing Quantity

**Finding:** Circuit quality is bounded by the minimum Phi across the qubits participating in the circuit, not by average or maximum Phi. Test 2 versus Test 3 is the cleanest internal demonstration: using the first available qubits above the 0.25 threshold at min-Phi = 0.254 produced only marginal results at approximately 1x error ratio, while selecting the best available HIGH-Phi qubits at min-Phi = 0.999 produced a clear 2.66x advantage. The routing rule is governed by min-Phi across the circuit qubits: execute on quantum hardware when min-Phi is sufficiently high, and otherwise fall back to classical or hybrid allocation depending on tractability. This weakest-link routing logic was supported across the 23 tested settings.

**Problem it solves:** Resource allocation needs a circuit-level stability criterion, not just per-qubit scores. Average qubit quality does not capture the risk that one bad qubit contaminates the whole circuit.

**Why it matters:** The weakest-link rule converts per-qubit Phi scores into a circuit routing signal. It also explains why barely-above-threshold qubit selections do not reliably produce good outcomes. The routing rule is therefore not threshold alone but threshold combined with best-available HIGH-Phi selection.

**Methodology:** All 23 tests used min-Phi as the circuit-level routing variable. Test 2 used first-available qubits above threshold. Test 3 used best-available qubits. Both tests used identical GHZ circuits and backends to isolate the effect of qubit selection quality within the above-threshold region.

**Evidence:** Test 2 at min-Phi 0.254: approximately 1x error ratio. Test 3 at min-Phi 0.999: 2.66x error ratio. The difference is not due to the threshold itself but to the quality margin above it.

**Negative results:** This result is strongest for small circuits where qubit contamination probability is manageable. The size-scaling results show that as circuit size grows, even best-available HIGH-Phi selection degrades because the probability of LOW-Phi contamination increases structurally with qubit count.

**Prior art position:** The cited prior work does not describe a circuit-level weakest-link routing rule based on minimum Phi across participating qubits applied as an execution policy on IBM Quantum hardware.

---

## Discovery 3: Under Patent-Grade Strict Methodology Across Seven Algorithms, HIGH-Phi Versus LOW-Phi Error Ratios Range From 2.90x to 30.47x

**Finding:** Seven quantum algorithms validated under strict patent-grade methodology show consistently large error separations between HIGH-Phi and LOW-Phi qubit selections. All seven show the same direction. The strict error ratios are: Bernstein-Vazirani 30.47x, Grover 15.90x, Deutsch-Jozsa 12.97x, QFT 8.12x, GHZ 7.38x, Simon's 4.07x, and QPE 2.90x. The same methodology that produced these results improved on the earlier research tests substantially. Grover improved from 3.27x under research methodology to 15.90x under strict methodology because research tests had not used truly negative-Phi LOW qubits.

**Problem it solves:** Research-grade tests with imprecise LOW qubit selection understate the true routing effect. The strict methodology isolated the effect by requiring verified physical qubit binding, true negative-Phi LOW selection where available, and decisive ground truth.

**Why it matters:** The algorithm-agnosticism result means the routing principle is not tied to one circuit family. It holds across oracle-based algorithms (BV, Deutsch-Jozsa, Grover, Simon's), phase-estimation-based circuits (QPE), transform-based circuits (QFT), and entanglement circuits (GHZ).

**Methodology:** Strict tests 17-23. Each test used a two-mode preflight and execute protocol. LOW-Phi qubits selected from connected qubit pool with negative-Phi values preferred. Physical binding verified via q.index after transpilation. Ground truth required to be at least 95% decisive or test aborted. Abort on transpiler remap. All real IBM hardware execution.

**Evidence:** Strict results: QPE 2.90x (HIGH-Phi 5.76% vs LOW-Phi 16.70%), Grover 15.90x (5.76% vs 91.60%), Deutsch-Jozsa 12.97x (5.76% vs 74.73%), Bernstein-Vazirani 30.47x (2.12% vs 64.72%), Simon's 4.07x (1.76% vs 7.15%), QFT 8.12x (1.66% vs 13.48%), GHZ 7.38x (3.88% vs 28.66%).

**Negative results:** Simon's Algorithm required a fallback LOW selection because no negative-Phi connected quad was available, making its 4.07x ratio the least rigorous of the strict results. QPE at 2.90x is the weakest ratio in the strict suite, showing that algorithm type affects effect size. The method is algorithm-agnostic in direction but not uniform in magnitude.

**Prior art position:** The cited prior work does not describe patent-grade validated HIGH-vs-LOW Phi execution separation across seven structurally distinct quantum algorithm families with verified physical qubit binding on IBM Quantum hardware.

---

## Discovery 4: Circuit Depth Creates a Hard Practical Boundary Where Quantum Execution Becomes Worse Than Random in the Tested Setting

**Finding:** In the tested depth-scaling experiments, quantum circuit error rose strongly with depth and crossed into worse-than-random territory at approximately depth 103. Error at depth 53 was 22.00%, at depth 103 was 67.29%, and at depth 203 was 64.06%. Error above 50% on a binary-style measurement task is worse than random guessing, making quantum execution uninformative rather than merely noisy.

**Problem it solves:** A routing decision that only considers qubit quality and ignores circuit depth will make wrong allocation decisions for deep circuits on even moderate-quality hardware.

**Why it matters:** This gives the routing framework a second input variable beyond Phi. Even qubits that pass the 0.25 threshold cannot sustain useful execution beyond certain depth in the tested setting. Classical fallback is not just better at extreme depth, it is the only option that produces reliable results.

**Methodology:** Test 8 depth-scaling experiment using identity-return circuits at depths 53, 103, and 203 gates on IBM Quantum hardware. Error measured as deviation from expected zero-error reference.

**Evidence:** Depth 53: 22.00% error. Depth 103: 67.29% error. Depth 203: 64.06% error. Both depth 103 and 203 exceed 50% error, the worse-than-random boundary for the tested circuit type.

**Negative results:** This boundary is established in the tested IBM setting and tested circuit type. The exact depth at which the worse-than-random transition occurs will depend on hardware, qubit quality, and circuit structure. The numbers reported here are not universal constants.

**Prior art position:** The cited prior work does not describe an empirically validated depth-based routing crossover boundary using a training-free scalar stability metric on IBM Quantum hardware.

---

## Discovery 5: Circuit Size Creates a Second Hard Practical Boundary Where LOW-Phi Quantum Execution Collapses Into Noise in the Tested Setting

**Finding:** In the tested size-scaling experiments, error rose from approximately 3% at 3 qubits to 94.85% at 15 qubits and 92.50% at 20 qubits. The transition from usable to noise-dominated was sharp: at 7 qubits the error was already 48.78% and at 10 qubits 22.29%. The repo treats the 15 and 20 qubit regimes as pure noise in the tested conditions. The probability calculation explains why this is structural: with approximately 12% of qubits being LOW-Phi at any time, a 20-qubit circuit has a 92% probability of containing at least one LOW-Phi qubit.

**Problem it solves:** A routing decision that only considers qubit quality and ignores circuit size will fail to catch the rapid reliability collapse that occurs as qubit count increases.

**Why it matters:** This turns the routing framework into a size-aware allocation system. The crossover from usable to noise-dominated happens within the 3 to 15 qubit range that is characteristic of current NISQ circuit experiments, making this operationally relevant rather than a theoretical boundary.

**Methodology:** Tests 5, 7, 10, and 14 used circuits of 3, 5, 7, 10, 15, and 20 qubits respectively on IBM Quantum hardware. Qubit selections used LOW-Phi hardware from the tested backends. Error measured as deviation from expected correct output.

**Evidence:** 3 qubits: approximately 3% error. 5 qubits: 7.20%. 7 qubits: 48.78%. 10 qubits: 22.29%. 15 qubits: 94.85%. 20 qubits: 92.50%.

**Negative results:** These numbers are from specific LOW-Phi qubit selections on IBM hardware in the tested conditions. The exact size boundary where collapse occurs depends on the quality of the available qubits, the circuit structure, and the hardware platform. The 15-qubit pure-noise result is specific to LOW-Phi conditions and should not be read as claiming that all 15-qubit quantum circuits produce noise.

**Prior art position:** The cited prior work does not describe an empirically validated size-based routing crossover boundary using a training-free scalar stability metric on IBM Quantum hardware with documented collapse to near-100% error at 15 to 20 qubits.

---

## Discovery 6: Error Varies Approximately 19x Across the Phi Spectrum, Supporting Phi as a Continuous Routing Variable Rather Than a Simple Pass-Fail Threshold

**Finding:** The threshold sweep test measured circuit error across a range of tested Phi values. Error ranged from approximately 55-60% at very low Phi values (-0.050 and 0.210) to 3.17% at Phi 0.999, a variation of approximately 19x across the tested Phi spectrum. The sweep also showed that one above-threshold case at Phi 0.730 still produced 39.97% error, demonstrating that simply clearing the 0.25 threshold does not guarantee low error.

**Problem it solves:** A binary threshold alone does not capture the strong continuous relationship between Phi and execution quality. The 19x variation across the spectrum supports using the actual Phi value as a routing risk gradient, not just a pass-fail check.

**Why it matters:** This result reframes the routing decision from a binary classification to a risk-proportional allocation. Higher Phi reduces error risk continuously, so the routing framework can be extended to probabilistic or cost-weighted allocation policies beyond simple threshold routing.

**Methodology:** Test 6 threshold sweep on ibm_fez. Five representative Phi values tested with GHZ circuits. Error measured as deviation from expected output across 8,192 shots per condition.

**Evidence:** Phi -0.050: 55.32% error. Phi 0.210: 60.33% error. Phi 0.474: 7.64% error. Phi 0.730: 39.97% error. Phi 0.999: 3.17% error. Variation from worst to best tested: approximately 19x.

**Negative results:** The above-threshold result at Phi 0.730 with 39.97% error is the most important negative finding in this test. It shows that threshold 0.25 is a necessary but not sufficient condition for low-error execution. Backend-specific factors including gate calibration quality independent of single-qubit Phi can still produce elevated error even for above-threshold qubits.

**Prior art position:** The cited prior work does not describe a threshold-sweep characterization of continuous error variation across the Phi spectrum on IBM Quantum hardware using a training-free formula.

---

## Discovery 7: The Routing Effect Is Reproducible and Transfers Across Three IBM Backends

**Finding:** Rerunning the primary crossover test produced the same qubit selection and nearly identical errors: HIGH-Phi error changed from 2.87% to 2.37% and LOW-Phi error changed from 8.23% to 8.11%, a difference of less than 0.5 percentage points on each. The method was also validated across ibm_fez, ibm_torino, and ibm_marrakesh in the multi-backend test, with the routing logic producing consistent direction across all three backends.

**Problem it solves:** A routing rule that works on one run or one device has limited operational value. Reproducibility and multi-backend support are required for deployment credibility.

**Why it matters:** The reproducibility result shows the Phi-based routing effect is a stable physical property of the hardware, not a lucky outcome of one calibration snapshot. The multi-backend result shows the routing principle is not a single-device artifact.

**Methodology:** Test 15 reproducibility test repeated Test 1 with the same qubit selection procedure. Test 4 multi-backend test ran identical circuits on ibm_fez, ibm_torino, and ibm_marrakesh with backend-specific HIGH-Phi and LOW-Phi qubit selections.

**Evidence:** Reproducibility: run 1 HIGH-Phi 2.87%, run 2 HIGH-Phi 2.37%, difference 0.50 percentage points. Run 1 LOW-Phi 8.23%, run 2 LOW-Phi 8.11%, difference 0.12 percentage points. Same qubits selected both runs. Multi-backend: routing direction confirmed on all 3 backends with backend-specific error magnitudes.

**Negative results:** Reproducibility is shown for the primary crossover case only, not exhaustively across all 23 tests. Backend-specific error magnitudes vary, so the quantitative routing benefit is not identical across backends. The method is reproducible in direction and approximate magnitude but not in exact error percentages.

**Prior art position:** The cited prior work does not describe reproducibility testing or multi-backend validation of a quantum-classical routing decision based on a training-free stability metric on IBM Quantum hardware.

---

## Cross-Repo Note: phi-hybrid-allocation Completes the Execution-Allocation Layer of the Quantum Phi Portfolio

quantum-phi-validation established that Phi predicts real quantum hardware quality: 445 qubits, 4.34x two-qubit gate error discrimination, 25-63x deep-circuit error discrimination, 83% error reduction from Phi-based qubit selection, and 4.42x GHZ entanglement error separation. phi-hybrid-allocation converts that diagnostic capability into an execution-allocation decision: when LOW-Phi quantum execution is worse than classical simulation, route to classical. quantum-phi-validation measures quality. phi-hybrid-allocation acts on it.

universal-phi-ml validated the minimum-Phi weakest-link rule under strict backend-split ML evaluation, showing 98.2% pair-quality transfer accuracy and that pair quality is bounded by the worst component. phi-hybrid-allocation operationalizes that same weakest-link structure as the circuit routing rule: min-Phi across circuit qubits determines whether to execute quantum or fall back.

The 30.47x Bernstein-Vazirani result and 7.38x GHZ result in phi-hybrid-allocation are the same seven-algorithm execution comparison numbers cited in Table 6 of the quantum-phi-validation Zenodo paper. Both numbers were generated by phi-hybrid-allocation and belong primarily to this repo. quantum-phi-validation references them as supporting evidence for its broader qubit-quality claims.

---

## Summary

| Discovery | Key Result |
|-----------|-----------|
| 1. Quantum-classical crossover | Classical 0.00% beats LOW-Phi 8.23% (GHZ), 64.72% (BV strict) |
| 2. Weakest-link routing | min-Phi 0.999 yields 2.66x advantage over min-Phi 0.254 |
| 3. Strict 7-algorithm validation | Ratios 2.90x (QPE) to 30.47x (Bernstein-Vazirani) |
| 4. Depth boundary | 67.29% error at depth 103, worse than random |
| 5. Size boundary | 94.85% error at 15 qubits, 92.50% at 20 qubits |
| 6. Continuous Phi spectrum | 19x error variation across tested Phi range |
| 7. Reproducibility and multi-backend | <0.50pp variation on rerun, validated on 3 backends |

---

**Patent:** US Provisional Application No. 63/956,752
**Filed:** January 9, 2026
**Paper DOI:** [10.5281/zenodo.20094713](https://doi.org/10.5281/zenodo.20094713)
**Repository:** https://github.com/Wise314/phi-hybrid-allocation
