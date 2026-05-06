# Task-Identity — Scientific Discoveries

**Patent #1 — Behavioral Drift Detection for Machine Learning Classification**

**Application Numbers:** 63/906,072 (filed October 27, 2025) and 63/981,437 (filed February 12, 2026)

**Repository:** https://github.com/Wise314/task-identity

**Paper:** [Zenodo DOI 10.5281/zenodo.20048912](https://doi.org/10.5281/zenodo.20048912)

---

## Discovery 1: Embedding Similarity and Behavioral Identity Measure Fundamentally Different Things and Diverge Catastrophically During Failure

**Finding:** Structural embedding similarity and Task-Identity are not correlated signals and decouple at exactly the moment that matters most. Embedding similarity remained at 0.583 during a complete model failure while Task-Identity correctly read 0.000. They measure different things: representational structure vs decision behavior.

**Evidence:** 99.3% to 0.0% accuracy collapse. Embedding similarity 0.583. Task-Identity 0.000. Gap of 58.3 percentage points.

---

## Discovery 2: Accuracy is Insufficient to Detect Behavioral Drift

**Finding:** A model can appear completely stable by traditional accuracy metrics while making fundamentally different decisions. This is empirically demonstrated, not hypothetical.

**Evidence:** Accuracy held at 93.6% to 93.7% while Task-Identity detected a 42.4% behavioral shift under class imbalance (Test 6).

---

## Discovery 3: Overall Behavioral Identity Scores Mask Catastrophic Class-Level Failures in Imbalanced Domains

**Finding:** Aggregate identity scores actively conceal failure. Per-class analysis is not optional in imbalanced domains. It is the only signal that works.

**Evidence:** 
- Vision (Test 4): overall Task-Identity 0.873, poisoned classes 0.17
- Financial extension (2.26M loans): overall Task-Identity 0.921, default class 0.000, representing 99.4% degradation in minority class detection

---

**Last Updated:** May 6, 2026
