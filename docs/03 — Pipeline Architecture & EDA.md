

This section describes the overall workflow structure (pipeline architecture) and the exploratory data analysis (EDA) performed before model training. These steps ensure that the dataset is well-understood, consistent, and aligned with the RUL prediction objective.

---

## 1. Data Loading

We begin by importing the NASA C-MAPSS FD001 dataset:

- Load `train_FD001.txt`, `test_FD001.txt`, and `RUL_FD001.txt`.
    
- Assign column names (engine ID, cycle, operational settings, sensor measurements).
    
- Verify shapes, types, and basic structure.
    

This step ensures that data is properly formatted for subsequent analysis.

---

## 2. Basic Data Exploration

We inspect the dataset to understand its characteristics:

- Statistical summary (`mean`, `std`, `min`, `max`)
    
- Distribution checks (histograms, KDE plots)
    
- Outlier ranges and consistency
    
- Count of engines and cycles per engine
    

This provides a high-level view of the dataset’s behavior.

---

## 3. Formulating Initial Hypotheses

Based on physical knowledge and prior research on C-MAPSS:

- Some sensors may show no degradation trend.
    
- Some signals might be redundant or highly correlated.
    
- Degradation becomes visible only near the end of engine life (~125 cycles).
    
- Stable operational settings should correspond to small variance in sensors.
    

These hypotheses will later be tested analytically.

---

## 4. Variance Analysis

We compute variance for each sensor:

`Variance(sensor_i) → Identify low-information features`

Sensors with extremely low variance (near-constant signals) are flagged for removal.

This reduces noise and improves training efficiency.

---

## 5. Correlation Analysis

A correlation matrix is used to:

- Detect highly correlated sensors (redundancy)
    
- Identify non-informative sensors
    
- Understand relationships between operational settings and sensor behavior
    

This helps prune the feature space and avoid overfitting.

---

## 6. Visualization & Trend Analysis

Multiple visual inspections are performed:

- Sensor evolution across cycles for individual engines
    
- Global degradation trends aligned by failure point (`t = 0`)
    
- Heatmaps, boxplots, density plots
    
- Comparison of sensor stability during the “healthy phase”
    

This step validates physical behavior and supports the RUL strategy.

---

## 7. Train vs Test Distribution Comparison

To ensure fair evaluation, we verify that both datasets share similar distributions:

- Sensor histograms (train vs test)
    
- Operational setting comparisons
    
- Range consistency
    

This avoids distribution shift problems during inference.

---

## 8. Degradation Trend Validation (Knee Point Analysis)

To justify the 125-cycle RUL cap:

- Align all engines at failure (`t = 0`)
    
- Analyze degradation-sensitive sensors (e.g., s11, s12)
    
- Identify the “knee point” where degradation becomes measurable
    

Result: degradation clearly appears only in the **last ~125 cycles**.

This confirms the piecewise-linear RUL target (`min(RUL_raw, 125)`).

---

## 9. Feature Reduction

Based on:

- Low variance
    
- Low correlation
    
- Absence of clear degradation trend
    
- Redundancy
    

we remove weak or noisy features to simplify the model input.