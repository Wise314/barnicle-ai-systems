# Cot Phi Stability

**Detect reasoning drift and false coherence in chain-of-thought outputs of large language models — without training data, ground truth labels, or verifier models**

**Status:** 🟢 **Provisional Patent Filed - Application #64/038,659 (Apr 14, 2026)**

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.20110938.svg)](https://doi.org/10.5281/zenodo.20110938)

**Paper:** [cot_phi_stability_paper.pdf](cot_phi_stability_paper.pdf) | [Zenodo DOI: 10.5281/zenodo.20110938](https://doi.org/10.5281/zenodo.20110938)
**Discoveries:** [Cot-Phi-Stability-Discoveries.md](Cot-Phi-Stability-Discoveries.md)

---

## 🚀 The Breakthrough

**Reasoning Chains Have No Mid-Generation Feedback.**

Large language models produce chain-of-thought reasoning that can look fluent and confident while containing logical breaks, contradictions, or fabricated intermediate steps. Existing approaches require trained classifiers, ground-truth labels, expensive verifier models, or task-specific evaluation pipelines.

**The result?** A training-free stability signal that detects two distinct reasoning failure regimes — false coherence and destabilized drift — using only the embeddings of the reasoning steps themselves.

---

## The Problem

### How Chain-of-Thought Quality Assurance Works Today

**Process Reward Models:**
- Trained classifier scores each reasoning step
- Requires labeled training data
- Domain-specific, doesn't transfer across reasoning types

**Self-Consistency:**
- Run N independent chains, take majority vote
- Brute-force compute, no mid-chain signal
- Cannot identify which chains are actually reliable

**Tree of Thoughts:**
- Use another LLM to evaluate branches
- Requires additional LLM calls per step
- No physical or thermodynamic grounding

**Semantic Entropy:**
- Cluster output samples for uncertainty estimate
- No identity component, no temporal coherence
- Single number per output, not per-step

### The Gap This Patent Fills

| Current Approach | Limitation | Our Solution |
|------------------|------------|--------------|
| Process reward models | Requires labeled training data | Training-free, no labels needed |
| Self-consistency | No mid-chain signal | Per-step stability monitoring |
| Tree of thoughts | Extra LLM calls per branch | Pure embedding-based computation |
| Semantic entropy | No identity or coherence terms | Three-component thermodynamic metric |
| Domain-specific evaluators | Don't transfer across reasoning types | Same formula across reasoning domains |

---

## Overview

This patent applies the same stability metric structure that has been evaluated on bearings, power grids, qubits, and biological signals to LLM chain-of-thought reasoning. The metric is computed from embeddings of each reasoning step and produces a per-step stability signal that distinguishes two reasoning failure regimes.

**Key Innovation:** A reasoning chain can fail in two thermodynamically distinct ways — false coherence (fluent but logically broken, Φ rises) and destabilized drift (semantic incoherence, Φ drops). Both regimes are detectable from embeddings alone.

---

## Validation Results

**Comprehensive Testing on Real Published Reasoning Datasets:**

| Test | Dataset | Purpose | Status |
|------|---------|---------|--------|
| Test 01 | GSM8K | Correct chains produce stable Φ | HARDENED |
| Test 02 | PRM800K | Φ drops at reasoning error step | HARDENED |
| Test 03 | PRM800K | Two failure regime separation | HARDENED |
| Test 04 | GSM8K | Restart-selection harness | NEGATIVE AT SCALE |
| Tests 05/05b/05c | PRM800K | Φ-weighted voting vs majority | NEGATIVE RESULT |
| Test 06 | Multi-domain | Cross-domain generalization | PASSED |
| Test 06b | GSM8K + HotpotQA + AQUA-RAT | Native rationale cross-domain | PASSED |
| Test 07 | swe-agent-trajectories | Agentic trajectory measurement | EXPLORATORY (honest negative on matched cohort) |
| Test 08 | ragbench expertqa | RAG grounding stability | HARDENED |

**All datasets are real and published. No synthetic data.**

---

## Key Findings

### Two Reasoning Failure Regimes Identified (Test 03)

On 10 problem pairs from PRM800K, two distinct thermodynamic regimes were cleanly separated using immediate error-step Φ delta:

| Regime | Pairs | Φ Delta at Error | Post-Error Φ | Interpretation |
|--------|-------|------------------|--------------|----------------|
| False coherence | 7/10 | -0.142 (rises) | 0.339 | Wrong reasoning remains stable |
| Destabilized drift | 3/10 | +0.283 (drops) | 0.189 | Wrong reasoning destabilizes chain |

**Φ does not just detect reasoning collapse — it characterizes the thermodynamic nature of the failure mode.**

### Cross-Domain Generalization (Test 06b)

Using native human-written rationales across three reasoning domains:

| Domain | Mean Φ | Φ Above Threshold |
|--------|--------|-------------------|
| GSM8K (math word problems) | 0.520 | Yes (zero warnings) |
| HotpotQA (multi-hop factual) | 0.412 | Yes |
| AQUA-RAT (quantitative reasoning) | 0.326 | Yes |

**Same formula, same threshold, same coupling constant — across three reasoning task families with no retraining or threshold adjustment.**

### RAG Grounding Stability (Test 08)

On rungalileo/ragbench expertqa data:

| Comparison | Mean Φ | Delta |
|------------|--------|-------|
| Grounded responses | 0.400 | — |
| Ungrounded responses | 0.321 | -0.079 |
| Supported sentences | 0.399 | — |
| Unsupported sentences | 0.343 | -0.056 |

**Ungrounded responses exhibited higher warning rates than grounded responses. Hardened with locked thresholds.**

### Honest Negatives Documented

This patent's validation includes negative results that bound the claim scope:

- **Tests 05, 05b, 05c:** Φ-based chain selection underperformed raw majority voting (70%) on PRM800K across three different selection strategies (40%, 40%, 50%). False coherence dominates this dataset and defeats naive Φ-based voting. **Conclusion:** Φ is a stability signal, not a standalone chain-selection criterion on this dataset.
- **Test 04:** 10-example prototype showed restart-selection signal; 50-example expansion produced honest negative (-2pp to -8pp). Prototype signal did not hold at scale.
- **Test 07:** Original result confounded by task identity. After matching to within-instance cohort, no directional Φ separation observed (delta -0.003). Honest negative on matched cohort.

**These negative results are reported alongside the positive findings as part of the scientific record.**

---

## The Core Formula

Φ = I × ρ - α × S

For chain-of-thought reasoning chains:

- **I (Identity)** — cosine similarity between each reasoning step embedding and the original question/goal
- **ρ (Coherence)** — autocorrelation between consecutive step embeddings
- **S (Entropy)** — mean pairwise dispersion among recent step embeddings
- **α = 0.1** — coupling constant
- **Φ_c = 0.25** — critical threshold

---

## Intervention Actions

| Φ State | Action |
|---------|--------|
| Φ ≥ 0.25 | CONTINUE — chain is stable |
| 0 < Φ < 0.25 | WARN — coherence degrading |
| Φ < 0 | INTERVENE — backtrack, branch, compress, or restart |

---

## Market Context

### LLM Reasoning Quality is an Unsolved Problem

As LLMs are deployed in agentic systems, reasoning-heavy applications, and high-stakes decision support, the quality of their chain-of-thought reasoning becomes critical. Existing approaches require expensive infrastructure (verifier models, training pipelines, labeled data) or provide only post-hoc evaluation.

**The unsolved problem:** No existing system provides a training-free, mid-generation stability signal that can both monitor reasoning quality and characterize the type of failure occurring.

### Where This Patent Fits

| Market Segment | Relevance | Our Advantage |
|----------------|-----------|---------------|
| LLM API providers | Direct — quality assurance layer | Training-free, black-box compatible |
| Agentic AI platforms | Reasoning reliability | Mid-chain failure detection |
| Multi-chain reasoning systems | Chain selection signal | Per-step stability monitoring |
| Production LLM deployments | Guardrail layer | No model access required |
| RAG systems | Grounding quality | Stability of reasoning over retrieved context |

---

## Benefits

### For LLM API Providers
- **Quality assurance layer:** Detect reasoning degradation without retraining
- **Black-box compatible:** Works with any LLM via output embeddings
- **No additional model calls:** Pure embedding computation
- **Reasoning-mode pricing tiers:** Premium tiers with stability guarantees

### For Agentic AI Platforms
- **Mid-chain failure detection:** Stop bad reasoning before it propagates
- **Trajectory monitoring:** Same formula across agent action sequences
- **Restart triggers:** Principled signal for chain restart vs continue
- **Multi-step reliability:** Per-step monitoring across long chains

### For Multi-Chain Reasoning Systems
- **Chain ranking signal:** Identify which chains are thermodynamically stable
- **Beyond majority vote:** Stability-aware aggregation
- **Failure mode characterization:** Distinguish false coherence from drift
- **Sample efficiency:** Better chain selection without more samples

### For Production LLM Deployments
- **Guardrail integration:** Block reasoning chains that fail stability check
- **Audit trail:** Per-step stability log for incident review
- **Domain agnostic:** Same threshold across reasoning task types
- **Operational visibility:** Quantitative reasoning quality metric

---

## Commercial Applications

### LLM Reasoning Quality Assurance
- Real-time chain-of-thought monitoring in production
- Per-step stability logging for audit and debugging
- Integration with API gateways for quality routing
- Customer-facing reasoning quality scores

### Agentic System Reliability Monitoring
- Trajectory stability monitoring for long-horizon agents
- Failure regime classification for incident analysis
- Restart vs continue decisions in agentic loops
- Integration with agent orchestration frameworks

### Multi-Chain Reasoning Selection
- Φ-aware chain ranking for reasoning ensembles
- Stability-weighted aggregation alternatives to majority vote
- Sample-efficient reasoning systems
- Foundation for two-axis chain selection frameworks

### RAG Grounding Verification
- Stability monitoring for retrieval-augmented generation
- Grounded vs ungrounded response detection
- Per-sentence stability for supported claim identification
- Citation quality scoring

---

## Cross-Domain Validation

This patent applies the same stability framework that has been evaluated across multiple physical and computational domains:

| Domain | Application | Key Result |
|--------|-------------|------------|
| Industrial | Bearing failure prediction | F1 up to 0.975 |
| Aerospace | Turbofan degradation | NASA C-MAPSS evaluated |
| Infrastructure | Power grid stability | UK 2019 blackout evaluated |
| Geophysical | Earthquake precursors | Tohoku M9.1 evaluated |
| AI/ML | Neural network training | 660 architectures, 99.7% precision |
| Quantum | Qubit stability scoring | 445 qubits, 83% error reduction |
| Quantum | Quantum-classical routing | 30.47x error improvement |
| Quantum | Real-time circuit intervention | 85.1% error reduction |
| LLM | Behavioral drift | r=-0.97, jailbreak detection |
| Biological | Cardiac arrhythmia | AUC 0.9148 |
| **LLM** | **Chain-of-thought reasoning (this patent)** | **Two failure regimes separated** |

**Same formula. Same threshold. Same coupling constant. Extended to LLM reasoning.**

---

## Relationship to Existing Portfolio

This patent extends the stability framework to a new domain (LLM reasoning) and builds on three closely related patents:

| Patent | Relationship |
|--------|--------------|
| LLM Behavioral Drift Detection (#63/973,673) | Embedding-based Φ computation method |
| Phi-Objective Controller (#63/984,704) | Intervention engine and action selection structure |
| Quantum Circuit Intervention (#63/973,723) | Structural blueprint for mid-execution closed-loop control |

**The framework is extended; the formula is unchanged.**

---

## Patent Strength

### What Makes This Patent Valuable

✅ **Real datasets:** GSM8K, PRM800K, HotpotQA, AQUA-RAT, ragbench, swe-agent-trajectories  
✅ **Multiple test types:** Baseline, drift detection, regime separation, cross-domain, RAG, agentic  
✅ **Hardened tests:** Locked thresholds, falsifiable assertions  
✅ **Honest negatives:** Bounded scope claims, scientific integrity  
✅ **Two-regime characterization:** Thermodynamic distinction between failure modes  
✅ **Training-free:** No labels, no classifier training, no verifier models  
✅ **Black-box compatible:** Works with any LLM via output embeddings  

### Competitive Moat

- **Training-free:** Works immediately, no data collection or labeling
- **Black-box:** Compatible with any LLM API
- **Cross-domain framework:** Same structure across many domains in portfolio
- **Failure regime characterization:** Distinguishes false coherence from drift
- **Honest validation:** Negative results documented, scope clearly bounded

---

## Target Customers

**LLM API Providers:**
- OpenAI, Anthropic, Google, Mistral, Cohere, AI21

**Agentic AI Platforms:**
- LangChain, LlamaIndex, AutoGen, CrewAI, Adept

**Reasoning-Focused AI Companies:**
- Companies building reasoning-heavy LLM applications

**Enterprise LLM Deployments:**
- Financial services, legal, healthcare, customer support

**RAG System Providers:**
- Companies building retrieval-augmented generation pipelines

---

## Validation Standards

✅ **Real published datasets only** — No synthetic data  
✅ **Locked thresholds** — Never tuned to results  
✅ **Falsifiable assertions** — All test outcomes documented  
✅ **Negative results documented** — Honest reporting throughout  
✅ **Multiple datasets per claim** — Cross-dataset validation  
✅ **Audit trails** — Full result outputs preserved  

---

## Patent Status

**Provisional Patent Filed:** April 14, 2026  
**Application Number:** 64/038,659  
**Confirmation Number:** 6968  
**Title:** Method and System for Detecting Reasoning Drift and False Coherence in Chain-of-Thought Outputs of Large Language Models Using a Stability Metric  
**Status:** Active, 12-month window for full utility patent  
**Claims:** 40 — covering monitoring, regime classification, and multi-chain selection  

---

## Repository

Full validation results and methodology documentation available at:  
**https://github.com/Wise314/cot-phi-stability**

---

## 📬 Contact

**Shawn Barnicle** — Independent Researcher & AI Systems Inventor

- 🌐 Website: [shunyatacafe.com](https://shunyatacafe.com)
- 📧 Email: ShawnBarnicle.ai@gmail.com
- 📧 Email: ShawnBarnicle@proton.me
- 💼 LinkedIn: [linkedin.com/in/shawn-barnicle-811887390](https://www.linkedin.com/in/shawn-barnicle-811887390)
- 🐙 GitHub: [Patent Portfolio](https://github.com/Wise314/barnicle-ai-systems) | [Physics Papers](https://github.com/Wise314/black-hole-information-paradox-resolution)

**Response Time:** 24-48 hours for licensing inquiries

---

## 📝 License

© 2025-2026 Shawn Barnicle. All Rights Reserved.

This document describes patented and patent-pending inventions. Viewing does NOT grant any license to use, implement, or commercialize these inventions. See [LICENSE](../LICENSE) for full terms.

---

**Last Updated:** May 10, 2026
**Patent Status:** Filed - U.S. Provisional Application No. 64/038,659 (April 14, 2026)
**Paper Published:** May 10, 2026 (Zenodo DOI 10.5281/zenodo.20110938)
**Validation:** GSM8K baseline 10/10 stable chains mean Phi 0.520, PRM800K matched pairs revealed two failure regimes (false coherence 7 of 10, destabilized drift 3 of 10), self-consistency selection negative result across three Phi-based strategies (40 to 50 percent vs 70 percent raw majority), cross-domain validation across math factual q
