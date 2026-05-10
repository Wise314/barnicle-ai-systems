# CoT Phi Stability Discoveries

**Patent #21:** Method and System for Detecting Reasoning Drift and False Coherence in Chain-of-Thought Outputs of Large Language Models Using a Stability Metric
**Provisional Application No.:** 64/038,659 (filed April 14, 2026)
**Repo:** cot-phi-stability
**Paper:** False Coherence: A Thermodynamic Failure Regime in Chain-of-Thought Reasoning
**Zenodo DOI:** [10.5281/zenodo.20110938](https://doi.org/10.5281/zenodo.20110938)

---

## Scope Note

This repo applies the Phi framework to reasoning chain stability monitoring in large language models. The domain-specific mapping of (I, rho, S) is different from the rest of the portfolio because the observable is sequential text steps rather than physical sensor readings or training trajectories: I (prompt-alignment) is cosine similarity between each reasoning step embedding and the original prompt embedding, clamped to [0, 1]; rho (local coherence) is cosine similarity between each step embedding and the previous step embedding, clamped to [0, 1], with rho_0 = 1.0 by convention; S (disorder) is the mean pairwise cosine distance among the most recent up to 5 step embeddings, normalized to [0, 1]. The same fixed-form Phi formula (Phi = I times rho minus alpha times S) with the same alpha = 0.1 and the same critical threshold Phi_c = 0.25 is used. Embeddings are computed with sentence-transformers/all-MiniLM-L6-v2 (pinned). All evidence in this entry is from real published datasets (GSM8K, PRM800K, HotpotQA, AQUA-RAT, OpenBookQA, ragbench, nebius/swe-agent-trajectories) with no synthetic data.

---

## Discovery 1: Stepwise Phi Monitoring Is Operational on Correct GSM8K Reasoning Chains With the Same Fixed-Form Phi Formula and Threshold

**Finding:** Applying the fixed-form Phi formula (Phi = I times rho minus 0.1 times S) with the same critical threshold Phi_c = 0.25 used across the portfolio to 10 GSM8K test examples with published gold rationales (fixed dataset indices 0, 1, 2, 3, 4, 5, 10, 25, 50, 100) produced 10 of 10 chains in the stable band throughout. Mean of mean Phi was 0.5205, mean of minimum Phi was 0.3884, total stable steps were 36, total warning steps were 0, and total collapse steps were 0. No parameter tuning, no threshold adjustment, no retraining.

**Problem it solves:** Before applying a stability metric to error transitions or to chain selection, the metric must produce sensible outputs on known-clean reference data. Without a documented baseline run on correct chains, downstream findings on incorrect chains cannot be interpreted.

**Why it matters:** This is the protocol-validation finding that establishes Phi is operational as a reasoning chain measurement instrument before any error-transition or selection results can be trusted. The mean Phi of 0.5205 sits comfortably above the threshold of 0.25 (margin of 0.27), confirming that correct mathematical reasoning chains stay in the stable band under the same fixed-form Phi formula used for bearings, quantum circuits, and neural training across the portfolio.

**Methodology:** GSM8K test split, 10 fixed dataset indices. Native gold rationale lines from the published dataset (Cobbe et al., 2021). Step embeddings computed with sentence-transformers/all-MiniLM-L6-v2 (pinned). For each step t: I_t = cosine(step_t embedding, prompt embedding) clamped to [0, 1]; rho_t = cosine(step_t embedding, step_{t-1} embedding) clamped to [0, 1] with rho_0 = 1.0; S_t = mean pairwise cosine distance among the most recent up to 5 step embeddings, normalized to [0, 1]. Phi_t = I_t times rho_t minus 0.1 times S_t. Step classification: stable if Phi_t at least 0.25, warning if 0 less than Phi_t less than 0.25, collapse if Phi_t less than 0.

**Evidence:** 10/10 chains stable. 36/36 steps stable. Mean of mean Phi 0.5205. Mean of minimum Phi 0.3884. Zero warnings. Zero collapses. Run date April 2, 2026. Test status HARDENED with locked thresholds.

**Negative results:** The result is bounded to 10 examples on one dataset (GSM8K test split). Whether the same baseline holds for other mathematical reasoning datasets, other models generating mathematical reasoning, or non-mathematical reasoning is not addressed by this test alone.

---

## Discovery 2: Immediate Previous-Step to First-Error-Step Phi Delta Separates Two Reasoning Failure Regimes (False Coherence and Destabilized Drift)

**Finding:** A two-regime classification of reasoning failure on PRM800K matched correct/incorrect chain pairs is established by computing the immediate Phi delta from the step just before the first labeled error step to the first labeled error step itself. Define delta_Phi = Phi(previous step) minus Phi(error step). When delta_Phi greater than 0, Phi dropped at the error step (destabilized drift: the chain degrades as it enters an incorrect state). When delta_Phi less than or equal to 0, Phi stayed flat or rose at the error step despite the chain becoming incorrect (false coherence: the chain stays locally coherent and on-topic while producing a wrong answer). The two regimes are also separated in their post-error behavior: mean post-error Phi for false-coherent chains was 0.339, while mean post-error Phi for destabilized-drift chains was 0.189, a gap of 0.150.

**Problem it solves:** Outcome-based evaluation of reasoning chains (whether the final answer is correct, majority voting, or a separately trained verifier) does not distinguish between qualitatively different failure modes within the chain itself. A method that classifies reasoning failures by their thermodynamic structure provides a different kind of diagnostic information than a binary correct-or-incorrect label.

**Why it matters:** The Phi quantities are computed independently of correctness labels, but regime classification requires an error-step signal such as human annotation or a process reward model output. Once a step-level correctness signal is available, the immediate-delta classification can be applied to characterize the failure mode without retraining. This distinction makes Discovery 3 and Discovery 4 interpretable as connected findings rather than isolated negative results.

**Methodology:** PRM800K train split (Lightman et al., 2023). 10 matched correct/drift pairs satisfying first_false_index at least 2 and n_steps at least 4. For each pair, identify the first step labeled incorrect by human annotation. Compute Phi at each step using the same formula and parameters as Discovery 1. Compute delta_Phi at the first error step. Classify per the rule above. Compute post-error Phi as the mean of Phi values from the first error step onward.

**Evidence:** Two regimes were cleanly separated by the immediate-delta classification. Mean delta_Phi for the false-coherence pairs was -0.142. Mean delta_Phi for the destabilized-drift pairs was +0.283. The 0.150 post-error Phi gap (0.339 vs 0.189) shows the two regimes also differ in their post-error trajectories. Run date April 3, 2026. Test status HARDENED with locked thresholds.

**Negative results:** The classification requires a step-level correctness signal. Without that, the immediate-delta classifier cannot identify which step is the error transition. The two-regime classification is a downstream diagnostic, not a standalone correctness oracle. Test 02 (broader before-versus-after segment averaging) and Test 03 (immediate previous-step to first-error-step delta) reach different conclusions on the same 10 pairs because they use different time windows; both findings are real and describe different time scales.

---

## Discovery 3: False Coherence Was the More Common Observed Failure Regime in the 10-Pair PRM800K Sample

**Finding:** Applying the two-regime classification from Discovery 2 to 10 matched correct/incorrect chain pairs from the PRM800K train split, false coherence (delta_Phi less than or equal to 0 at the first labeled error step) was observed in 7 of 10 pairs. Destabilized drift (delta_Phi greater than 0 at the first labeled error step) was observed in 3 of 10 pairs. False coherence was therefore the more common observed regime in this 10-pair sample. The 7-of-10 proportion is an observation in this sample, not a demonstrated general rate across other datasets, models, or reasoning types.

**Problem it solves:** Without identifying which failure regime is more common, naive applications of stability-based methods to chain selection or intervention cannot anticipate the dominant failure mechanism. If destabilized drift were the more common regime, naive Phi filtering or weighting would correctly down-weight wrong chains. If false coherence is the more common regime, naive Phi-based methods can amplify wrong chains because false-coherent wrong chains score high on the same dimensions Phi measures.

**Why it matters:** This empirical observation is the mechanistic explanation for the negative selection results in Discovery 4 and the negative intervention result in Discovery 5. False coherence is wrong reasoning that stays prompt-aligned, locally coherent, and low-disorder; this means it scores high on Phi while being incorrect. A selection method that rewards high Phi cannot distinguish a false-coherent wrong chain from a correct chain on the basis of Phi alone. The 7-of-10 observation closes the causal chain from "Phi is a stability signal" to "Phi alone is not a sufficient correctness oracle on this dataset."

**Methodology:** Same 10 matched pairs as Discovery 2 (PRM800K train split). Same immediate-delta classification. Count of pairs in each regime computed directly from the per-pair delta_Phi values.

**Evidence:** 7 of 10 pairs classified as false coherence. 3 of 10 pairs classified as destabilized drift. Fraction false coherence: 0.70. Run date April 3, 2026. Test status HARDENED.

**Negative results:** The 7-of-10 proportion is from a single sample of 10 matched pairs from one dataset. The paper itself flags this explicitly as an observation, not an established population rate.

---

## Discovery 4: Three Variants of Phi-Based Self-Consistency Selection All Underperformed Raw Majority Voting on PRM800K, With False Coherence Amplification as the Mechanistic Explanation

**Finding:** Three variants of Phi-based self-consistency chain selection were tested on the same 10 PRM800K prompt groups (mean 10.7 chains per prompt). All three underperformed raw majority voting at 70% accuracy: Phi-weighted majority voting (where each chain's vote is weighted by mean Phi) reached 40% accuracy (a 30 percentage point loss); a hard Phi gate (mean_phi at least 0.25 AND collapse_steps equal to 0) reached 40% accuracy with 4 of 10 prompts abstaining because all chains fell below threshold; a softer collapse-only filter reached 50% accuracy with 2 of 10 prompts abstaining. Each softer use of Phi reduced harm but none crossed zero relative to raw majority. In all 3 cases where Phi-weighted voting and raw majority disagreed, Phi-weighted selected the wrong answer.

**Problem it solves:** Without the explicit selection-failure result, the false coherence finding (Discovery 3) would be a structural observation without practical consequence. With this result, the false coherence observation gains operational meaning: stability-as-a-correctness-proxy fails on this dataset because false-coherent wrong chains receive high Phi values and are amplified by selection methods.

**Why it matters:** This is the result that establishes Phi as a stability signal but not a standalone correctness oracle on PRM800K. The mechanistic explanation is direct: false-coherent wrong chains stay prompt-aligned, locally coherent, and low-disorder, so they score high on Phi. Any selection rule that rewards high Phi can be defeated by false-coherent wrong chains receiving the reward. The three variants form a monotone trend (40%, 40%, 50%) below the 70% baseline, consistent with progressively reducing the amplification effect but not eliminating it. Future improvements require combining Phi with a separate correctness signal rather than using Phi alone.

**Methodology:** PRM800K train split, 10 prompt groups with at least 2 fully correct chains per prompt. Embeddings computed with sentence-transformers/all-MiniLM-L6-v2 (pinned). Phi formula and parameters as in Discovery 1. Three selection rules: Phi-weighted majority where vote weight equals chain mean Phi; hard gate filtering chains by mean_phi at least 0.25 AND collapse_steps equal to 0 before voting; collapse-only filter excluding chains with at least one collapse step. Raw majority baseline: each chain casts an equal-weight vote.

**Evidence:** Phi-weighted: 40% accuracy vs 70% raw majority, delta -0.30, prompts where votes differ 3 of 10, Phi-weighted wins 0, Phi-weighted losses 3. Hard gate: 40% accuracy, abstentions 4 of 10. Collapse-only filter: 50% accuracy, abstentions 2 of 10. Run date April 4, 2026. Phi-weighted result verified stable across two independent runs with two different code versions.

**Negative results:** This discovery is itself a documented set of three negative results. PRM800K chains in this sample cluster near or below Phi_c = 0.25 (mean of mean Phi values were not separated cleanly above the threshold), unlike GSM8K gold chains which averaged 0.520; this is what makes the hard gate produce 4 of 10 abstentions. The result does not show that Phi is uninformative; it shows that stability alone is not sufficient as a correctness proxy on PRM800K when false coherence is the more common regime.

---

## Discovery 5: Phi-Based Restart-Selection Intervention Did Not Scale From a 10-Example Prototype to a 50-Example Run, Producing a Documented Self-Falsification

**Finding:** A 10-example prototype of restart-selection intervention on GSM8K showed promising signal: a collapse-only restart policy reached +0.10 accuracy gain with 1 win and 0 losses; a warning-or-collapse restart policy reached +0.20 accuracy gain with 2 wins and 0 losses. A 50-example expansion produced an honest negative result: collapse-only reached -0.02 (3 wins, 4 losses); warning-or-collapse reached -0.08 (4 wins, 8 losses); the warning-or-collapse policy restarted 49 of 50 chains. The prototype signal did not hold at scale. Restart chains were systematically worse than baseline chains on this generation model (Qwen/Qwen2.5-1.5B-Instruct) and prompt setup. This is a self-falsification: a result that looked positive at small scale did not survive expansion.

**Problem it solves:** Without scale-testing and a documented honest negative, a small-scale positive result on stability-based intervention could be misread as validated active control. The 10-vs-50 comparison makes the boundary explicit.

**Why it matters:** This is the boundary finding for active CoT intervention in this repo. The validated scientific claims of Patent #21 are measurement (Discovery 1), regime characterization (Discoveries 2 and 3), the negative selection results that explain why naive intervention fails (Discovery 4), and this scale-tested negative on intervention itself. The repo does not claim a validated CoT controller, and the 10-vs-50 contrast is the documented evidence supporting that scope.

**Methodology:** GSM8K test split. Generation model Qwen/Qwen2.5-1.5B-Instruct. Embedding model sentence-transformers/all-MiniLM-L6-v2 (pinned). Phi formula and parameters as in Discovery 1. Two policies: collapse_only restart (restart chains with at least one collapse step); warning_or_collapse restart (restart chains with at least one warning or collapse step). 10-example prototype: 10 fixed dataset indices. 50-example expansion: 50 fixed dataset indices (200-445 step 5).

**Evidence:** 10-example prototype: baseline accuracy 0.10, restart accuracy 0.20, mean baseline mean Phi 0.270, baseline warnings 9 of 10, baseline collapses 4 of 10. 50-example expansion: baseline accuracy 0.20, restart accuracy 0.12, mean baseline mean Phi 0.257, baseline warnings 47 of 50, baseline collapses 24 of 50. Run dates April 4, 2026 (prototype) and April 5, 2026 (expansion). Test status NEGATIVE AT SCALE (documented).

**Negative results:** This discovery is itself a documented honest negative. The intervention is not validated at 50 examples on this model and prompt setup. The mechanistic explanation is consistent with Discoveries 3 and 4: baseline mean Phi sits near Phi_c = 0.25 (0.257), so the warning threshold fires almost everywhere (47 of 50), causing restart to be triggered on most chains. Phi-based restart selection without a correctness signal cannot reliably improve outcomes on this dataset.

---

## Discovery 6: Cross-Domain Phi Measurement Is Operational Across Mathematical, Factual, Quantitative, and Science Reasoning Formats With the Same Formula and Threshold

**Finding:** The same fixed-form Phi formula with alpha = 0.1 and Phi_c = 0.25 produces above-threshold mean Phi across multiple reasoning domain formats with no parameter tuning within these tested samples. Test 06 on three domains: GSM8K mathematical reasoning mean Phi 0.520, HotpotQA multi-hop factual reasoning mean Phi 0.412, OpenBookQA science reasoning mean Phi 0.425. Test 06b (stronger integrity version using native human-written rationales for two of three domains) on three domains: GSM8K mean Phi 0.520, HotpotQA mean Phi 0.412, AQUA-RAT quantitative reasoning mean Phi 0.326. All values are above the threshold of 0.25 on mean Phi. Stability strength varies across domains: GSM8K gold rationales produce zero warnings and zero collapses; HotpotQA and AQUA-RAT produce non-zero warnings and collapses, with AQUA-RAT noisier than the others.

**Problem it solves:** A reasoning stability metric validated only on mathematical reasoning has limited general applicability. Cross-domain measurement at multiple reasoning formats establishes that the metric is operational beyond a single domain.

**Why it matters:** This finding establishes Phi as a runnable cross-domain measurement instrument across mathematical, factual, quantitative, and science reasoning. The qualifier is important: cross-domain measurement is operational, but Phi is not a correctness oracle (Discovery 4). The 0.520 to 0.326 range across domains is meaningful: the metric is sensitive to the specific format and embedding-friendliness of each domain, while still placing all four domain types above the critical threshold on mean Phi.

**Methodology:** Test 06: GSM8K (math, gold rationales), HotpotQA (extracted supporting-fact sentences from human-annotated context, not native rationales), OpenBookQA (science reasoning), 10 examples per domain, fixed dataset indices. Test 06b: GSM8K (math, native gold rationales), HotpotQA (extracted support sentences), AQUA-RAT (quantitative reasoning, native human-written rationales), 10 examples per domain. Same Phi formula, same alpha = 0.1, same Phi_c = 0.25, same embedding model.

**Evidence:** Test 06: GSM8K mean Phi 0.520, mean min Phi 0.409, 0 warnings, 0 collapses; HotpotQA mean Phi 0.412, mean min Phi 0.197, 7 warnings, 1 collapse; OpenBookQA mean Phi 0.425, mean min Phi 0.070, 7 warnings, 3 collapses; overall mean Phi 0.452. Test 06b: GSM8K mean Phi 0.520; HotpotQA mean Phi 0.412; AQUA-RAT mean Phi 0.326, mean min Phi 0.061, 15 warnings, 5 collapses; overall mean Phi 0.419. Run dates April 5 and April 6, 2026. Test status PASSED for both.

**Negative results:** The chain types are not identical across the tested domains. GSM8K and AQUA-RAT use native human-written rationales; HotpotQA uses extracted supporting-fact sentences. This is a heterogeneous cross-domain measurement, not a controlled comparison of identical chain formats. AQUA-RAT noise likely reflects limitations of the embedding model when handling mathematical notation and symbolic expressions, not a failure of the Phi framework. Sample size is 10 examples per domain.

---

## Discovery 7: Phi Shows Directional RAG Grounding Signal at the Response and Sentence Level but No Matched-Cohort Agentic Success-vs-Failure Separation After Audit Correction

**Finding:** This finding has two layers that should not be conflated.

**Layer 1 (RAG grounding, directional signal):** Phi distinguished grounded from partially ungrounded RAG responses on the rungalileo/ragbench expertqa subset (10 grounded vs 10 partially ungrounded responses, fixed stream indices). Response-level: grounded mean Phi 0.400 vs ungrounded 0.321, delta +0.079; warning rates 0.345 vs 0.473. Sentence-level: supported sentences mean Phi 0.399 vs unsupported 0.343, delta +0.056. All three locked thresholds met.

**Layer 2 (agentic trajectory matching, null result after audit):** An initial Test 07 result on nebius/swe-agent-trajectories produced an apparent positive signal (delta +0.086) that an audit traced to task-identity confounding (the original harness compared success trajectories from one task against failure trajectories from a different task). When corrected to a within-instance matched cohort (all 20 trajectories from the same instance Melevir__cognitive_complexity-15, frozen stream indices), no directional separation was observed: success mean Phi 0.337 vs failure 0.340, delta -0.003; warning rates 0.270 vs 0.257.

**Problem it solves:** Without separating the RAG positive from the agentic null and without documenting the audit correction, the entry could either overclaim agentic detection (using the original confounded result) or hide the real RAG signal (by combining everything as mixed evidence). Two layers, clearly separated, preserve the actual evidentiary state.

**Why it matters:** The RAG layer establishes directional evidence that Phi is sensitive to grounding status at both the response and sentence level on a published dataset with locked thresholds. The agentic layer establishes a documented self-falsification: an initial result that an audit invalidated, with the corrected matched-cohort result preserved as the honest evidentiary record. Documenting it strengthens the patent's audit credibility rather than weakening it.

**Methodology:** Layer 1 (RAG): rungalileo/ragbench expertqa subset, test split, streaming. 10 fully grounded responses, 10 partially ungrounded responses. Each multi-sentence response treated as a monitored step sequence with the question as the prompt. Sentence-level analysis compares per-example mean Phi at supported sentences vs unsupported sentences. Run date April 8, 2026. Layer 2 (agentic): nebius/swe-agent-trajectories train split, streaming. 10 successful trajectories and 10 failed trajectories, all from the same instance Melevir__cognitive_complexity-15 (within-instance matched cohort). Each trajectory treated as a step sequence with the task description as the prompt. Run date April 9, 2026 (rerun after audit fix).

**Evidence:** Layer 1 RAG response-level: grounded mean Phi 0.400, mean min Phi 0.275, mean warning rate 0.345; ungrounded mean Phi 0.321, mean min Phi 0.273, mean warning rate 0.473; delta +0.079. Sentence-level: supported sentences mean Phi 0.399, unsupported sentences mean Phi 0.343, delta +0.056. Test status HARDENED. Layer 2 agentic matched cohort: success mean Phi 0.337, mean warning rate 0.270, mean n_steps 20.9; failure mean Phi 0.340, mean warning rate 0.257, mean n_steps 14.6; delta -0.003 on mean Phi. Test status EXPLORATORY (honest negative on matched cohort).

**Negative results:** The RAG response-level comparison is across different questions; grounded and ungrounded examples may differ in question type or response length in ways that independently affect Phi. The sentence-level analysis is not a strict within-response matched causal test. Both RAG results are directional evidence from a small sample (10 examples per group), not definitive findings. The agentic null is a single matched cohort within one task instance with 20 total trajectories. The original (confounded) Test 07 result is preserved as audit history but is not part of any current claim.

---

## Summary

| Discovery | Topic | Result Type |
|-----------|-------|------------|
| 1 | GSM8K baseline (correct chains stable) | Hardened positive |
| 2 | Two-regime classification (false coherence vs destabilized drift) | Hardened positive |
| 3 | False coherence dominant in PRM800K sample (7 of 10) | Hardened positive |
| 4 | Three Phi-based self-consistency selectors underperform raw majority | Documented negative |
| 5 | Phi-based restart intervention scale-falsified (10 to 50 examples) | Documented self-falsification |
| 6 | Cross-domain operational across math, factual, quantitative, science | Hardened positive |
| 7 | RAG directional positive at response and sentence level; agentic null after audit | Two-layer (positive + null) |

---

**Inventor:** Shawn Barnicle
**Email:** ShawnBarnicle.ai@gmail.com
**GitHub:** https://github.com/Wise314
**Patent Status:** Provisional - Pending. © 2025-2026 Shawn Barnicle. All Rights Reserved.
