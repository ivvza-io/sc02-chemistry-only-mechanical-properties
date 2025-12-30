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
> When paired with empirically observed uncertainty, via out-of-fold error, this signal can support robust design reasoning such as defining conservative composition margins and reducing non-conformance risk.

---

## Scope and Constraints

### In scope
- Chemistry-only feature set (composition variables)
- UTS prediction as the primary target
- Group-aware evaluation to avoid leakage across related heats/conditions
- Empirical uncertainty estimation based on out-of-fold error
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

3. **Uncertainty and robustness**
   - Use out-of-fold residuals to estimate realistic prediction error
   - Translate empirical error into conservative safety margins for design reasoning


4. **Interpretability**
   - Identify dominant chemistry drivers (global + local where helpful)
   - Translate into engineering-relevant insights (not feature importance theater)

5. **Design artifacts**
   - Design maps (2D chemistry spaces) showing:
     - expected UTS (p50)
     - conservative regions based on empirical error margins


6. **Inverse design - illustrative**
   - Illustrate feasibility screening in chemistry space under conservative constraints
   - Emphasis on robustness and constraint satisfaction, not numerical optimization

---

## Expected Outputs

### Tables
- Baseline vs ML comparison (MAE, RMSE, R²) under GroupKFold
- Out-of-fold error statistics used as conservative margins
- Summary of top chemistry drivers

### Figures
- Predicted vs actual (out-of-fold)
- Residual diagnostics (out-of-fold)
- Design maps: expected UTS (p50) and conservative regions (p50 − margin)

### Decision-ready artifacts
- Robust region definition (e.g., p10 ≥ specification target)
- Clear “use conditions” and “do not use” boundaries

---

## Evaluation Criteria

A model is considered useful if it demonstrates:

- **Consistent predictive signal** across group folds (not just overall fit)
- **Stable and unbiased out-of-fold error**, suitable for conservative decision-making
- **Actionable interpretability** (stable chemistry drivers, plausible metallurgy)
- **Decision value**: ability to define conservative regions and margins under constraints


Not considered success criteria:
- maximizing R² via aggressive tuning
- highly complex models without interpretability
- single-number reporting without uncertainty

---

## Exit Condition

This study case is complete when:

- A chemistry-only baseline and a simple ML model are implemented and evaluated
- Group-aware results are reproducible from the semantic layer
- Out-of-fold error is quantified and used as an empirical safety margin
- A consistent set of conservative design maps is generated
- Findings are summarized into decision-oriented guidance

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

