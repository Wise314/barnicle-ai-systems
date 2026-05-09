# LLM Phi Stability — Scientific Discoveries

**Patent:** US Provisional Application No. 63/973,673
**Filed:** February 2, 2026
**Title:** Method and System for Detecting Behavioral Drift and Safety Guardrail Degradation in Generative Language Models Using a Stability Metric
**Repository:** https://github.com/Wise314/llm-phi-stability
**Paper:** [10.5281/zenodo.20098298](https://doi.org/10.5281/zenodo.20098298)

---

## Scope

This repo validates the Universal Phi framework on generative language models. The patent's central novelty is a black-box, output-only stability metric Phi = I × rho - alpha × S that operates without access to hidden states, attention weights, or token probability distributions. Evidence comes from 8 validation tests on real inference (no synthetic generations), covering TinyLlama 1.1B as the primary test bed and Phi-2 (2.7B) as the multi-billion-parameter scale validation. Baseline reproducibility was verified by a protocol check (Test 1: Phi = 1.0, I = 1.0, S = 0.0 on self-comparison) before the drift-detection tests below were run. Tests 3, 6, 7, and 8 are fully hardened (embedding revision pinned, per-output hashes, manifest SHA256, canonical prompts enforced). Test 4 is partially hardened. Tests 2 and 5 are not hardened and are carried as supporting evidence.

---

## Discovery 1: Phi Detects Quality Degradation Induced by Temperature

**Finding:** Raising sampling temperature from 0.7 to 1.5 on TinyLlama produces a 0.282 drop in Phi, confirming that the metric detects output quality degradation when generation becomes more stochastic.

**Problem it solves:** Prior quality-drift detection methods for LLMs typically rely on reference-answer comparison, log-likelihood scoring, or human evaluation. The cited prior art does not describe a training-free scalar that combines identity preservation, temporal coherence, and sampling entropy into a single drift signal observable in a matched-condition comparison.

**Why it matters:** Establishes the baseline empirical claim that Phi is a valid drift signal on LLMs before extending to safety, scale, fine-tuning, and adversarial detection tests. All downstream LLM discoveries depend on this result.

**Methodology:** TinyLlama 1.1B-Chat-v1.0 executed the same prompt suite at two temperatures. Baseline condition: temperature 0.7 (typical generation). Degraded condition: temperature 1.5. Phi computed from output embeddings using sentence-transformers/all-MiniLM-L6-v2.

**Evidence:** Baseline Phi = 0.964 (I = 0.991, S = 0.274). Degraded Phi = 0.682 (I = 0.717, S = 0.348). Absolute delta -0.282.

**Negative results:** Test 2 is not hardened with the full audit protocol (no embed revision pinning, no per-output hashes, no manifest). Claim is bounded to the tested temperature range (0.7 to 1.5) and the tested model (TinyLlama 1.1B).

**Prior art position:** No cited prior art describes the Phi = I × rho - alpha × S three-term scalar applied to output embeddings as a quality-drift signal.

---

## Discovery 2: Phi Detects Safety-Regime Behavioral Drift With Refusal Rate Co-Tracking

**Finding:** On safety-sensitive prompts, raising temperature from 0.2 to 1.0 produces a 0.072 drop in Phi accompanied by refusal rate collapse from 14% to 3%, showing that Phi co-tracks a safety-relevant behavioral signal rather than generic quality variance.

**Problem it solves:** Prior LLM safety monitoring typically uses classifier-based refusal detection, prompt-filter matching, or content moderation APIs trained on labeled examples. The cited prior art does not describe a training-free scalar that detects safety-regime behavioral drift from output behavior alone.

**Why it matters:** This is the primary safety application for the repo. Refusal-rate collapse under increased temperature is a known LLM failure mode. Demonstrating that Phi tracks this signal without training, without classifier labels, and without access to model internals establishes Phi as an operational safety-monitoring signal.

**Methodology:** TinyLlama 1.1B-Chat-v1.0 executed a 20-prompt safety-sensitive suite at 5 samples per prompt (200 generations). Baseline condition: temperature 0.2. Degraded condition: temperature 1.0. Phi computed from output embeddings. Refusal rates recorded from the generated outputs.

**Evidence:** Baseline Phi_low = 0.985 (I_drift = 0.939), refusal rate 14%. Degraded Phi_high = 0.912, refusal rate 3%. Delta Phi = -0.072. Delta refusal = -11 percentage points. Smoke run on 40 generations corroborated the direction (Phi = 0.985 vs 0.870, delta -0.115) before full validation.

**Negative results:** Test is bounded to the tested prompt suite and tested temperature range. Whether the Phi-refusal co-tracking generalizes to out-of-distribution safety categories or adversarial prompt suites is not established by this test.

**Prior art position:** No cited prior art describes a training-free output-only scalar that co-tracks Phi with refusal behavior on a safety-focused prompt suite.

---

## Discovery 3: Phi Generalizes to Multi-Billion-Parameter Models Including Phi-2 (2.7B)

**Finding:** Phi-2 (2.7B parameters, 2.5x larger than TinyLlama) reproduces the Test 3 safety-regime drift pattern at smaller absolute delta (Delta Phi = -0.044 vs TinyLlama's -0.072), confirming that the Phi methodology transfers from TinyLlama to Phi-2 with the same formula and the same output-only evaluation protocol.

**Problem it solves:** Prior LLM-drift work is often validated on a single model family, leaving scale generalization unestablished. The cited prior art does not demonstrate that a training-free stability metric transfers from small research models to multi-billion-parameter models with the same formula and protocol.

**Why it matters:** 2.7B parameters is within the range of multi-billion-parameter open-weight models. Validating Phi at that scale establishes the methodology is not restricted to small research models.

**Methodology:** microsoft/phi-2 (2.7B parameters) executed the Test 3 safety-sensitive prompt suite at 10 prompts, 3 samples (60 generations). Same protocol, same temperatures (0.2 and 1.0), same embedding model as Test 3 on TinyLlama. Phi computed from output embeddings.

**Evidence:** Phi-2 baseline Phi_low = 0.992 (I_drift = 0.966), refusal rate 73%. Phi-2 degraded Phi_high = 0.948, refusal rate 53%. Delta Phi = -0.044. Delta refusal = -20 percentage points. The smaller Delta Phi magnitude than TinyLlama is consistent with more stable behavior on the tested Phi-2 model under the same perturbation; broader scale trends are not established here.

**Negative results:** Single-model scale validation at 2.7B. Whether Phi continues to scale to 7B, 13B, or 70B models is not established. The smaller Delta Phi on Phi-2 means detection sensitivity decreases on Phi-2 relative to TinyLlama under matched conditions, which is a real scope bound.

**Prior art position:** No prior-art method demonstrates cross-scale generalization of a training-free output-embedding stability scalar at the 2.7B parameter scale.

---

## Discovery 4: Temporal Coherence Quantified by rho Is Stable Across Sequential Evaluation Windows

**Finding:** The rho component of Phi yields a stable value (rho = 0.989) across six sequential evaluation windows on TinyLlama under steady-state conditions, confirming that rho captures temporal coherence and does not drift spontaneously in the absence of a perturbation.

**Problem it solves:** For a temporal-coherence term to be useful as part of a drift-detection signal, it must remain stable in the absence of drift. Prior drift-detection work rarely separates temporal coherence from output variance.

**Why it matters:** Establishes that rho is a usable drift-detection component. If rho drifted under steady-state conditions, it would confound downstream drift claims in Tests 2, 3, 7, and 8. The 0.989 steady-state value supports rho as the temporal term in the Phi = I × rho - alpha × S structure.

**Methodology:** TinyLlama 1.1B-Chat-v1.0 executed a fixed prompt suite across six sequential evaluation windows under matched conditions. rho computed as a measure of I stability across windows. Phi computed per window.

**Evidence:** rho_global_mean = 0.989. Phi_global_mean = 0.949. corr(window, S) = -0.57 indicating some within-window variance but not cross-window drift. Per-window hashes logged for reproducibility.

**Negative results:** Steady-state test only. The six-window design is a minimal baseline; longer observation windows or under-load conditions are not tested here.

**Prior art position:** No cited prior art provides a bounded rho term with an empirically validated steady-state baseline on LLMs.

---

## Discovery 5: Phi Tracks Temperature Precisely Across a Wide Range

**Finding:** Across 8 temperature values from 0.3 to 1.7, Phi correlates with temperature at r = -0.97, confirming that the metric tracks a known source of output variation with high precision.

**Problem it solves:** Prior LLM-drift work does not establish a continuous dose-response relationship between a training-free stability scalar and a tunable generation parameter.

**Why it matters:** The r = -0.97 correlation demonstrates that Phi is a continuous measurement of output stability, not just a binary drift detector. This supports downstream operational controls such as adaptive temperature reduction or early-warning systems.

**Methodology:** TinyLlama 1.1B-Chat-v1.0 executed a fixed prompt suite at 8 temperatures (0.3, 0.5, 0.7, 0.9, 1.1, 1.3, 1.5, 1.7). Phi, I, and S computed per temperature. Pearson correlation computed across the 8 temperature points.

**Evidence:** corr(temp, Phi) = -0.97. corr(temp, S) = +0.93 (entropy rises with temperature). corr(temp, I) = -0.96 (identity falls with temperature). All three correlations are consistent with the expected physical interpretation of the Phi components.

**Negative results:** Test 5 is not hardened. The 8-point correlation is strong but bounded to single-model single-prompt-suite evaluation. Claim is that Phi tracks temperature precisely in the tested setting, not that this correlation holds across all prompt domains.

**Prior art position:** No cited prior art establishes a continuous correlation between a training-free output-embedding stability metric and temperature.

---

## Discovery 6: Phi Discriminates Between Tested Model Architectures and Checkpoints by 0.775

**Finding:** Comparing TinyLlama 1.1B-Chat (Phi = 0.973) to GPT-2 (Phi = 0.198) produces a 0.775 Phi gap, far larger than within-model drift deltas from Tests 2, 3, 7, and 8, confirming that Phi discriminates meaningfully between the tested model architectures and checkpoints.

**Problem it solves:** A drift-detection signal that cannot distinguish different models from each other provides weak evidence of behavioral-content measurement. Cross-model discrimination shows that Phi captures information beyond configuration parameters.

**Why it matters:** Large cross-architecture Delta Phi relative to within-model drift deltas supports the claim that Phi measures model-behavior content, not just generation randomness. Establishes upper-bound magnitude for within-configuration drift detection.

**Methodology:** TinyLlama 1.1B-Chat-v1.0 and GPT-2 (125M parameters) executed a fixed prompt suite under matched conditions (same temperature, same sampling parameters, same prompt suite). Phi computed per model. All model revisions pinned; manifest SHA256 logged.

**Evidence:** TinyLlama Phi = 0.973 (I = 1.0, S = 0.271). GPT-2 Phi = 0.198 (I = 0.225, S = 0.270). Absolute delta 0.775. S is nearly identical across models (0.271 vs 0.270), so the delta is driven almost entirely by the identity component I.

**Negative results:** TinyLlama and GPT-2 differ in architecture, parameter count (1.1B vs 125M), and training data. The 0.775 Delta Phi reflects their combined differences, not architecture alone. Claim is bounded to "Phi distinguishes the tested model architectures and checkpoints" rather than "Phi separates any architecture pair" generally.

**Prior art position:** Cross-model drift comparison is not a documented feature in the cited prior-art cluster. No cited prior art establishes a cross-model training-free scalar comparison at this magnitude.

---

## Discovery 7: Phi Detects Fine-Tuning Drift Between Base and Chat Checkpoints

**Finding:** Comparing TinyLlama's base checkpoint (step-1431k-3T) to its instruction-tuned Chat-v1.0 checkpoint produces Delta Phi = -0.254 on the same prompt suite under matched conditions, demonstrating that Phi detects fine-tuning-induced behavioral shift without retraining, without access to training logs, and without labeled examples of drift.

**Problem it solves:** Prior fine-tuning drift detection typically requires training-time monitoring, held-out evaluation sets, or model-internal probes. The cited prior art does not describe a training-free output-only method for detecting instruction-tuning behavioral shift.

**Why it matters:** Instruction tuning is a standard production step. A method that detects its behavioral footprint from output embeddings alone provides a deployment-time drift monitor that operates without access to training infrastructure.

**Methodology:** TinyLlama 1.1B-intermediate-step-1431k-3T (base) and TinyLlama 1.1B-Chat-v1.0 (instruction-tuned) executed the same 20-prompt suite at 5 samples each (200 generations total) under matched conditions (temperature 0.7, same seeds). Phi computed per model. Hardened with canonical prompt enforcement, plan/prompt/system hashes, and manifest SHA256.

**Evidence:** Base Phi_base = 0.981. Fine-tuned Phi_ft = 0.727. I_drift = 0.743 (26% identity shift). Delta Phi = -0.254. Refusal rate 4% (base) vs 1% (ft). Delta refusal = -3 percentage points, indicating instruction tuning slightly reduced refusals on the tested prompt suite.

**Negative results:** Single model-family test (both checkpoints are TinyLlama). Whether Phi detects fine-tuning drift across different model families at similar magnitudes is not established. The 26% identity shift does not distinguish beneficial fine-tuning (capability gain) from harmful drift (capability loss), only that behavior has shifted.

**Prior art position:** Catastrophic-forgetting detection methods in the cited prior art typically operate at training time. None describe a deployment-time training-free output-embedding approach.

---

## Discovery 8: Phi Detects Behavioral Drift Under Adversarial Prompt Injection With Fairness-Controlled Seeds

**Finding:** Applying a prompt-injection wrapper to TinyLlama Chat-v1.0 produces Delta Phi = -0.207 under fairness-controlled conditions (same seeds in both conditions), demonstrating that Phi detects adversarial behavioral drift from output embeddings alone, without input-embedding comparison and without access to model internals.

**Problem it solves:** Existing injection detection methods either compare input-prompt embeddings or monitor token-level entropy. The cited prior art does not describe an output-embedding detection approach that operates in black-box settings where only generated text is available.

**Why it matters:** This is the primary differentiation point for the patent. The output-embedding approach operates where input-embedding methods cannot (when input embedding access is unavailable or unreliable) and where token-logprob methods cannot (when logprobs are not exposed by the model API). Commercial deployment scenarios often have only text outputs available.

**Methodology:** TinyLlama 1.1B-Chat-v1.0 executed a 20-prompt suite at 5 samples each (200 generations total) in two conditions: normal (no injection) and with a standardized injection wrapper prepended to each prompt. Same seeds in both conditions to control for sampling variance. Same temperature (0.7). Same prompt suite. Phi computed per condition. Hardened with canonical prompts, plan/prompt/system/injection hashes, manifest SHA256, trust_remote_code locked, embedder fingerprint recorded, and mean vector hashes for drift re-derivation.

**Evidence:** Normal Phi_normal = 0.971. Injected Phi_injection = 0.763. I_drift = 0.793 (21% identity shift). Delta Phi = -0.207. Refusal rate 6% (normal) vs 4% (injection), Delta refusal = -2 percentage points. Leak rate 12% (normal) vs 6% (injection), Delta leak = -6 percentage points.

**Negative results:** Single injection-wrapper design tested. Whether Phi detects other adversarial attack patterns (multi-turn jailbreaks, encoded payloads, persona manipulation) is not established by this test. The Delta Phi = -0.207 magnitude does not establish a universal detection threshold.

**Prior art position:** No cited prior art describes a black-box output-only injection-drift detector using a training-free three-term stability scalar.

---

## Methodology Note: Black-Box Output-Only Audited Protocol

The protocol uses only generated text outputs. No hidden states, attention weights, or token probability distributions are required. Compatible with any LLM accessible by API or text generation call. An external embedding model (sentence-transformers/all-MiniLM-L6-v2) provides the embedding space used for I and S computation, keeping the detection stack decoupled from the monitored model.

Phi = I × rho - alpha × S with alpha = 0.1. I computed from cosine similarity between baseline-configuration outputs and comparison-configuration outputs. rho computed as a measure of temporal stability of I across sequential evaluation windows; for single-snapshot evaluation, rho is set to 1.0. S computed from variation among multiple sampled outputs under fixed configuration. Specific implementation details of the rho and S computations are documented in the repository's core/phi_llm.py reference implementation.

Audit binding: prompts embedded in test scripts and SHA256 hashed before execution. Plans precommitted with all generation parameters locked. PRE audit written before inference with plan_hash, prompts_hash, and generation_params. POST audit written after inference with output manifest hashes. Manifest SHA256 logged for tamper detection. Phi logged only and never gates pass/fail at the protocol layer. Real inference only; no synthetic or fabricated generations.

Baseline reproducibility confirmed by Test 1: TinyLlama 1.1B-Chat-v1.0 compared against itself with identical seeds yields Phi = 1.0, I = 1.0, S = 0.0, establishing that the protocol is implemented correctly before drift-detection tests are run.

Hardening status: Tests 3, 6, 7, and 8 are fully hardened (embedding revision pinned, per-output hashes, manifest SHA256). Test 4 is partially hardened (embedding revision pinned and per-output hashes logged, no manifest). Tests 2 and 5 are not hardened and are carried as supporting evidence.

---

## Summary

| Discovery | Key Result |
|-----------|-----------|
| 1. Quality drift under temperature | Delta Phi -0.282 (TinyLlama, 0.7 to 1.5) |
| 2. Safety-regime drift with refusal co-tracking | Delta Phi -0.072, refusal 14% to 3% |
| 3. Scale validation on Phi-2 (2.7B) | Delta Phi -0.044, refusal 73% to 53% |
| 4. rho steady-state stability | rho = 0.989 across 6 windows |
| 5. Continuous temperature tracking | corr(temp, Phi) = -0.97 across 8 points |
| 6. Cross-architecture discrimination | Delta Phi 0.775 (TinyLlama vs GPT-2) |
| 7. Fine-tuning drift detection | Delta Phi -0.254, 26% identity shift |
| 8. Adversarial injection detection | Delta Phi -0.207, 21% identity shift |

---

**Patent:** US Provisional Application No. 63/973,673
**Filed:** February 2, 2026
**Paper DOI:** [10.5281/zenodo.20098298](https://doi.org/10.5281/zenodo.20098298)
**Repository:** https://github.com/Wise314/llm-phi-stability
