

This document defines the performance standards and testing protocols used to validate the Hybrid CNN–GRU model before deployment.  
It ensures that evaluation is **rigorous**, **safety-driven**, and **aligned with industrial predictive maintenance requirements**.

---

## 1. Quantitative Metrics (Evaluation Scores)

The model is evaluated using a combination of **standard regression metrics** and **industry-specific safety metrics**.

---

### A. RMSE — Root Mean Squared Error (Global Accuracy)

**Definition:**  
Measures the standard deviation of prediction errors.

RMSE=1n∑(ypred−ytrue)2RMSE = \sqrt{\frac{1}{n}\sum (y_{pred} - y_{true})^2}RMSE=n1​∑(ypred​−ytrue​)2​

**Interpretation:**  
Average error in number of cycles.

**Acceptance Target:**

`RMSE ≤ 16 cycles`

---

### B. NASA Score — Industrial Safety Metric (Primary Criterion)

**Definition:**  
Asymmetric metric used in the PHM’08 competition.  
Penalizes errors differently depending on whether they are early or late.

d=ypred−ytrued = y_{pred} - y_{true}d=ypred​−ytrue​

If prediction is **early** (safe):

S=e−d/13−1,d<0S = e^{-d/13} - 1, \quad d < 0S=e−d/13−1,d<0

If prediction is **late** (dangerous):

S=ed/10−1,d≥0S = e^{d/10} - 1, \quad d \ge 0S=ed/10−1,d≥0

**Philosophy:**

- Late predictions (overestimating RUL) are **dangerous** → heavy penalty.
    
- Early predictions (underestimating RUL) are safe → mild penalty.
    

**Acceptance Target:**

`NASA Score ≤ 400`

---

### C. R² — Coefficient of Determination (Explainability)

**Definition:**  
Proportion of variance in the RUL explained by the model.

**Acceptance Target:**

`R² ≥ 0.80`

Ensures the model captures meaningful degradation physics rather than noise.

---

## 2. Visual Validation (Critical Diagnostic Plots)

Numerical scores alone are insufficient.  
Three diagnostic visualizations are required for qualitative validation.

---

### A. Sorted Performance Curve (S-Curve)

**Method:**  
Sort engines by true RUL and overlay predictions.

**Success Criteria:**

- **Critical zone (RUL < 50):** predictions should follow the ground truth closely.
    
- **Healthy zone (RUL > 125):** predictions should form a plateau (respecting the capped RUL target).
    

---

### B. Single Engine Trajectory Plot

**Method:**  
Plot predictions across the full life of a selected engine.

**Success Criteria:**

- Smooth decreasing trend (no oscillations or jumps)
    
- Anticipation of end-of-life
    
- Coherent behavior across cycles
    

---

### C. Overfitting Diagnostic (Loss Curves)

**Method:**  
Compare Train Loss vs Validation Loss over epochs.

**Success Criteria:**

- Curves should converge together
    
- No divergence (validation rising while training decreases)
    
- If divergence appears → model rejected
    

---

## 3. Acceptance Criteria (Go / No-Go Checklist)

Before deployment, the candidate model must satisfy **all** the following conditions:

| Criterion                 | Acceptance Threshold             |
| ------------------------- | -------------------------------- |
| **Accuracy (RMSE)**       | ≤ 16 cycles                      |
| **Safety (NASA Score)**   | ≤ 400                            |
| **Model Size**            | < 20k parameters                 |
| **Positive Outputs Only** | Guaranteed via ReLU              |
| **Stable Training**       | Loss curves convergent           |
| **Safe Predictions**      | Minimal late-life overestimation |

If **any** of these criteria are not met, the model is not accepted for deployment.

---

## 4. Evaluation Process Summary

`1. Prepare test windows (last-sequence only) 2. Predict RUL for every test engine 3. Apply same RUL cap (125 cycles) 4. Compute RMSE, MAE, R², NASA Score 5. Generate diagnostic plots (S-curve, trajectory, loss curves) 6. Compare metrics to acceptance thresholds 7. Validate or reject model according to Go/No-Go checklist`