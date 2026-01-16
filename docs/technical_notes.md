# Technical Notes — SC02  
**Chemistry-Only Modeling of UTS (AA3105-O)**  
**Audit-Level Methodology, Diagnostics, and Design Decisions**

---

## 1. Scope and Role of This Document

These technical notes document the analytical rationale, methodological decisions, and diagnostic evidence supporting **SC02 — Chemistry-Only Modeling of UTS (AA3105-O)**.

They complement:
- the **public README**, which focuses on decision framing and engineering implications,
- and the **notebooks**, which contain full analytical execution, experiments, and figures.

This document is intended for:
- technical audit,
- methodological traceability,
- and long-term reproducibility.

It is **not** intended as a narrative summary, tutorial, or marketing artifact.

---

## 2. Analytical Objective (Restated Precisely)

The analytical objective of SC02 is to determine whether **chemical composition alone** provides sufficient and stable predictive signal to support the **definition of conservative internal UTS control limits** for AA3105-O.

The goal is **not** to predict mechanical properties for release decisions, but to:
- identify chemistry regions associated with robust UTS behavior,
- provide a quantitative starting point for internal standard definition,
- and reduce reliance on costly and poorly targeted physical trial campaigns.

This study intentionally stops short of defining finalized numerical limits. Its role is to validate feasibility and characterize uncertainty, leaving final limit selection to subsequent uncertainty-aware tools (SC05).

### Explicit Non-Objectives
This analysis does **not** aim to:
- replace mechanical testing,
- produce release-grade predictions,
- model full process–microstructure–property relationships,
- or optimize nominal accuracy at the expense of robustness.

---

## 3. Data Contracts and Grain Definition

### 3.1 Analytical Unit (Grain)

- **Grain:** one row per heat  
- **Justification:**
  - Chemical composition is defined at heat level.
  - Purchase orders, laboratory tests, and production units are all traceable to heat identifiers.
  - Internal standards are defined at the **alloy and temper level** and evaluated using heat-level data as the analytical unit.
  - Prevents leakage from repeated measurements of the same heat.

This grain choice reflects how decisions are made operationally and is consistent across the portfolio.

---

### 3.2 Dataset Construction

- **Source:** SQL semantic layer defined in SC01.
- **System:** AA3105-O only.
- **Inputs:**
  - Chemical composition (Mn, Mg).
  - Tensile test results (UTS).
- **Aggregation:**
  - Multiple tensile tests per heat aggregated to heat-level statistics.
- **Filters applied:**
  - Valid chemistry records.
  - Valid tensile test results.
- **Exclusions:**
  - None related to chemistry completeness; all heats included in this study have complete chemistry records.

These transformations enforce a deterministic, reproducible analytical dataset suitable for model evaluation.

---

### 3.3 Domain of Validity

Supported chemistry domain (approximate, based on observed data):
- **Mg:** ~0.20–0.71 %
- **Mn:** ~0.31–0.60 %

Supported mechanical response:
- **UTS:** ~100–140 MPa

Predictions or standards derived outside this domain are considered **unsupported extrapolation** and explicitly out of scope.

---

## 4. Validation and Leakage Control

### 4.1 Validation Strategy

- **Method:** Group-aware cross-validation (GroupKFold).
- **Grouping variable:** heat identifier.
- **Rationale:**
  - Prevents leakage from correlated samples sharing the same production context.
  - Reflects real deployment conditions where future heats are unseen.

All reported results are based exclusively on **out-of-fold (OOF)** predictions.

---

### 4.2 Leakage Risks Considered

Potential leakage vectors:
- Multiple tensile tests per heat.
- Shared processing context within heats.

Mitigations:
- Aggregation to heat-level grain.
- Group-aware validation.

Residual risks:
- Temporal correlation between adjacent heats is not explicitly modeled.

This residual risk is accepted as representative of real industrial data conditions.

---

## 5. Modeling Choices and Rationale

### 5.1 Model Classes Evaluated

- **Ridge regression**
  - Linear baseline.
  - Regularized, stable coefficients.
  - Smooth response surface suitable for standards.
- **Polynomial regression (degree 2)**
  - Introduces limited, controlled non-linearity.
- **Random Forest**
  - High-flexibility reference model.
  - Used diagnostically to assess potential upper bounds on fit.

Tree-based models were **not selected** as the basis for standards despite improved raw accuracy due to:
- reduced smoothness of response surfaces,
- higher sensitivity to local data density,
- lower suitability for defining stable, communicable control limits.

---

### 5.2 Hyperparameters and Constraints

- Ridge regularization strength fixed after preliminary stability checks.
- Polynomial degree limited to 2 to avoid over-fitting.
- No extensive hyperparameter optimization performed.

This reflects the objective of **robust standard definition**, not metric maximization.

---

## 6. Evaluation Metrics and Interpretation

### 6.1 Metrics Used

- **Median MAE (MPa):** central error tendency.
- **P95 absolute error (MPa):** tail risk indicator.

Tail metrics are prioritized because:
- internal standards exist to prevent **rare but costly failures**,
- average accuracy alone does not capture operational risk.

---

### 6.2 Metric Trade-offs

- Improvements in MAE without corresponding reduction in tail error are not considered meaningful.
- R² is reported for context but is not used as a decision criterion.

This aligns evaluation with engineering risk rather than statistical convenience.

---

## 7. Diagnostics and Sensitivity Checks

- Fold-level MAE and P95 statistics assessed for stability.
- No single fold dominates error behavior.
- Residuals are centered with no strong systematic bias.
- Chemistry drivers remain consistent across folds.

Sensitivity to minor chemistry perturbations is implicitly assessed through cross-validation rather than synthetic stress testing.

---

## 8. Known Limitations and Failure Modes

- Chemistry does not capture all strengthening mechanisms.
- Error magnitude varies across composition space.
- Historical process conditions constrain applicability.
- Drift in upstream processes may invalidate **model outputs** over time, requiring revalidation of derived standards.

These limitations motivate:
- generalization testing (SC03),
- variable screening (SC04),
- explicit uncertainty treatment (SC05).

---

## 9. Decision Implications

This analysis supports:
- initiation of **internal chemistry-based UTS control limits**,
- early-stage risk screening before committing production resources,
- prioritization of physical trials when chemistry lies outside robust regions.

It must **not** be used for:
- product release decisions,
- quality certification,
- replacement of mechanical testing.

---

## 10. Design Decisions Log

Key non-obvious decisions recorded for traceability:

- Started with chemistry after observing empirical correlation during EDA, later supported by metallurgical reasoning (solid solution strengthening and deformation effects).
- Selected UTS as the initial target due to its lower sensitivity to microstructural variation and thickness effects in AA3105-O.
- Restricted portfolio scope to UTS despite modeling YS and elongation internally, to reduce cognitive load and focus the narrative.
- Chose conservative, interpretable models over higher-accuracy learners to ensure standards could be communicated and trusted.
- Deferred inclusion of process variables to a dedicated screening study (SC04).

---

## 11. Traceability

- **Notebook:** `sc02_chemistry_only_uts_model.ipynb`
- **Data semantics:** SQL semantic layer defined in SC01
- **Commit:** `pendiente`

---

### Closing Note

These technical notes document *why* chemistry-only modeling was considered a defensible starting point for internal UTS standard definition, and *how* methodological restraint was applied to preserve robustness, interpretability, and industrial relevance.