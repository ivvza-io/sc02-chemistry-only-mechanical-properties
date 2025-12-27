# Study Case 2 — AA3105-O: Chemistry-Only UTS Model

## Position in the Portfolio

This study case builds directly on Study Case 1 ([SQL reproducibility foundation](https://github.com/ivvza-io/sql-analytics-case-study)) and establishes the first modeling baseline, relying entirely on the reproducible semantic layer defined there.

It tests whether **chemistry alone** contains enough predictive signal to support **robust engineering design decisions**, before introducing any process or geometry variables.

---

## Core Question

**Using chemistry only, can we produce calibrated, decision-relevant predictions for UTS that are useful for robust design under real specification constraints?**

---

## Thesis

> Even with chemistry alone, there is meaningful and calibratable predictive signal for UTS, sufficient to support risk-aware engineering decisions.
> When paired with uncertainty estimates (e.g., prediction intervals), this signal can support robust design decisions such as defining safe process margins and reducing non-conformance risk.

---

## Scope and Constraints

### In scope
- Chemistry-only feature set (composition variables)
- UTS prediction as the primary target
- Group-aware evaluation (to avoid leakage across related heats/conditions)
- Uncertainty outputs (prediction intervals and calibration)
- Interpretability sufficient to support engineering insight (not just performance)

### Out of scope (explicit)
- Adding process or route variables (reserved for later study cases)
- Domain-specific microstructure proxies beyond chemistry
- Extensive hyperparameter tuning aimed at leaderboard performance
- Multitarget models (e.g., UTS + YS + elongation) unless required by data availability

Although the underlying modeling framework can be extended to other mechanical properties
(e.g., yield strength and elongation), this study case focuses on UTS as a representative target.
This allows a controlled evaluation of chemistry-only predictive signal
without introducing additional domain-specific complexity.


---

## Data Inputs (from the semantic layer)

- Chemistry composition records for heats (chemistry-only features)
- Mechanical test results providing UTS target values
- Required join keys and grain guarantees from SC1 semantic views

**Assumptions:**
- Chemistry and UTS are correctly linked at the intended grain (one row per heat/test definition)
- Chemistry variables are within a reasonable domain (outliers handled explicitly)

---

## Method Overview

1. **Baseline modeling**
   - Simple baseline (e.g., linear / ridge) as a reference
   - One ML baseline (e.g., tree-based) for non-linear signal capture
   - Keep modeling choices conservative and interpretable

2. **Validation strategy**
   - Group-aware cross-validation (e.g., GroupKFold) aligned with industrial reality
   - Report performance distributions across folds, not just averages

3. **Uncertainty and calibration**
   - Produce prediction intervals (e.g., p10/p50/p90)
   - Evaluate calibration (coverage) and sharpness (interval width)

4. **Interpretability**
   - Identify dominant chemistry drivers (global + local where helpful)
   - Translate into engineering-relevant insights (not feature importance theater)

5. **Design artifacts**
   - “Design maps” (2D chemistry spaces) showing:
     - predicted UTS quantiles
     - uncertainty/risk regions

6. **Inverse design - illustrative**
   - Illustrate how to search chemistry regions that satisfy robust targets
   - Emphasis on feasibility and constraints, not optimization novelty

---

## Expected Outputs

### Tables
- Baseline vs ML comparison (MAE, RMSE, R²) under GroupKFold
- Interval metrics: coverage (p10–p90) and average width
- Summary of top chemistry drivers

### Figures
- Predicted vs actual with uncertainty
- Calibration plots (coverage vs nominal)
- Design maps: p10 / p50 / p90
- Risk/uncertainty maps (interval width)

### Decision-ready artifacts
- Robust region definition (e.g., p10 ≥ specification target)
- Clear “use conditions” and “do not use” boundaries

---

## Evaluation Criteria

A model is considered useful if it demonstrates:

- **Consistent predictive signal** across group folds (not just overall fit)
- **Reasonable calibration** of prediction intervals (coverage near nominal)
- **Actionable interpretability** (stable chemistry drivers, plausible metallurgy)
- **Decision value**: ability to define robust regions and margins under constraints

Not considered success criteria:
- maximizing R² via aggressive tuning
- highly complex models without interpretability
- single-number reporting without uncertainty

---

## Exit Condition

This study case is complete when:

- A chemistry-only baseline and a simple ML model are implemented and evaluated
- Group-aware results are reproducible from the semantic layer
- Prediction intervals are produced and assessed for calibration and width
- A consistent set of design maps and robust regions is generated
- Findings are summarized into decision-oriented guidance (what this enables / what it cannot)

---

## Anti-Scope (What This Study Case Does Not Do)

- No process-aware modeling
- No large feature expansion
- No multi-variable design tools beyond chemistry space
- No “production deployment” work (focus is analytical demonstration)

These are intentionally deferred to later study cases.

---

## Relationship to Next Study Case

Study Case 2 establishes the chemistry-only baseline.

Study Case 3 will reuse the same methodology across additional alloy/temper systems to test generalization and identify where chemistry is sufficient vs where process dominates.

