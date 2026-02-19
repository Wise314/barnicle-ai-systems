# Stability Metric Foundations — Papers

Peer-reviewed preprints establishing the mathematical and empirical foundations behind the patent portfolio.

---

## Papers

### A Stability Index for Cross-Domain Degradation Detection

**DOI:** [10.5281/zenodo.18523292](https://doi.org/10.5281/zenodo.18523292) | February 2026

The core validation paper. Documents the stability index across 31 independent systems in 6 engineered domains — mechanical bearings, turbofan engines, power grids, earthquake precursors, neural networks, and superconducting qubits — using a single formula with fixed parameters. No per-domain tuning. The same threshold established on bearing vibration data in October 2024 correctly separated stable from failing regimes across all 31 systems tested through January 2026. Includes biological extension achieving AUC 0.90 on cardiac arrhythmia detection.

**Key results:**
- 31/31 correct classification across 6 engineered domains
- Same parameters (α=0.1, threshold 0.25) work in all domains — no tuning
- UK 2019 blackout, Tohoku M9.1, Parkfield M6.0, San Simeon M6.5 all identified from pre-event data
- 445 qubits validated across 3 IBM Quantum backends
- Cardiac arrhythmia AUC 0.90, within 0.07 of specialist HRV metrics
- Sensitivity analysis confirms parameters are constrained, not arbitrary

---

### Thermodynamic Stability Metric Provides Early Warning of Qubit Degradation on IBM Quantum Hardware

**DOI:** [10.5281/zenodo.18522745](https://doi.org/10.5281/zenodo.18522745) | February 2026

The quantum early warning paper. Demonstrates that the stability metric serves as a leading indicator of qubit degradation — providing days of advance warning before IBM's own systems flag problems. Establishes that the coherence ratio (T2/T1), excluded from prior qubit-selection heuristics, is actually the dominant predictor of qubit quality over time.

**Key results:**
- 100% detection rate — zero missed degradation events
- Average 6.8 days early warning, maximum 20 days
- Coherence ratio accounts for 70-78% of predictive power
- 98.4% cross-backend transfer without retraining
- 30.47x error discrimination between high-quality and low-quality qubits
- 83% error reduction using metric-based qubit selection

---

### Φ = I × ρ − α × S: A Domain-Agnostic Stability Metric and Autonomous Controller

**DOI:** [10.5281/zenodo.18684052](https://doi.org/10.5281/zenodo.18684052) | February 2026

The comprehensive framework paper. Unifies all prior validation into a single fixed-form metric evaluated across eight domains, and introduces the Φ-Objective Controller — an autonomous control engine that uses Φ as a safety constraint (not an optimization target) with three anti-Goodhart safeguards to prevent proxy gaming.

**Key results:**
- 8 domains validated: mechanical, aerospace, electrical, geophysical, neural, quantum, cardiac, LLM
- 42 systems across 9 domains, 100% classification accuracy
- Controller Phase 2E: 65% win rate (13/20 seeds), no catastrophic regressions
- Kill-only monitoring: 99.7% precision across 660+ architectures
- Anti-Goodhart safeguards: performance floor, step-wise rejection, correlation monitor
- 15 provisional patents unified under one framework

---

## Why These Papers Matter

These are not theoretical proposals. They document empirical results on real hardware and real catastrophic events using published datasets. The methodology is fully described, the parameters were fixed before cross-domain testing, and the code is available for replication.

Together, they establish the scientific foundation for all 15 patents in the portfolio.

---

## 📬 Contact

**Shawn Barnicle** — Independent Researcher & AI Systems Inventor

- 📧 Email: ShawnBarnicle.ai@gmail.com
- 📧 Email: ShawnBarnicle@proton.me

---

## 📝 License

© 2025-2026 Shawn Barnicle. All Rights Reserved.
