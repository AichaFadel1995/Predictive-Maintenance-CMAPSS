


# RUL Concept – Summary

## 1. Definition

The **Remaining Useful Life (RUL)** is the estimated number of operating cycles before an equipment reaches failure.  
In this project, it corresponds to **the number of flight cycles remaining before a turbofan engine fails**.

Two RUL types in the NASA C-MAPSS dataset:

- **Ground-Truth RUL (Test Set)**  
    Provided in `RUL_FD001.txt` and used **only for evaluation**.
    
- **Training RUL (Supervised Target)**  
    Computed from each run-to-failure engine:
    
    `RUL_raw = MaxCycle(engine) – CurrentCycle`
    

---

## 2. Issue: Healthy Phase Paradox

Using the raw RUL directly (e.g., 300 → 0) creates a fundamental mismatch:

- Sensor signals remain **stable and constant** during early engine life.
    
- Yet the model is asked to learn a **continuously decreasing** target.
    

This leads to:

- Overfitting on noise
    
- Unstable training
    
- Poor generalization
    

---

## 3. Solution: Piecewise Linear RUL (Cap = 125 Cycles)

To align learning with physical behavior, the RUL target is rectified:

`RUL_target = min(RUL_raw, 125)`

Interpretation:

- **Healthy Phase (RUL > 125):**  
    Target capped at 125 → system stable, no degradation detectable.
    
- **Degradation Phase (RUL ≤ 125):**  
    Target follows real countdown → degradation becomes measurable.
    

This piecewise strategy stabilizes training and improves prediction accuracy.

---

## 4. Why 125 Cycles? (Data-Driven Justification)

A knee-point analysis of degradation-sensitive sensors (e.g., **s11**, **s12**) aligned at failure (`t = 0`) shows that:

- The degradation trend becomes **visibly measurable**
    
- Only in the **last ~125 cycles** before failure.
    

Thus, the 125-cycle cap is **physically consistent** and **empirically validated**.