# 

This section details all technical transformations applied to the NASA C-MAPSS FD001 dataset before feeding it into the RUL prediction model.

---

## 1. Data Cleaning

We prepare the dataset by:

- Removing low-information or near-constant sensors
    
- Removing redundant highly correlated sensors
    
- Ensuring consistent indexing by engine
    
- Checking and cleaning missing values (rare in FD001)
    

---

## 2. Compute Raw RUL (Training Set)

For each run-to-failure engine:

`RUL_raw = MaxCycle(engine) – CurrentCycle`

This represents the natural countdown to failure.

---

## 3. Apply Piecewise Linear RUL (Target Rectification)

We apply the 125-cycle cap, consistent with the degradation knee point:

`RUL_target = min(RUL_raw, 125)`

- Healthy phase → capped at 125
    
- Degradation phase → real countdown
    

This becomes the **supervised learning target (y)**.

---

## 4. Feature Scaling (Optimized for GRU/Tanh)

To stabilize training and suit GRU activation functions (`tanh`), we scale all features using:

`MinMaxScaler(feature_range=(-1, 1))`

Why this choice?

- GRU/GRU+CNN architectures work best when inputs match the range of **tanh → (-1, 1)**
    
- Avoids saturation
    
- Improves gradient flow
    
- Faster convergence
    

Scaling is fit **only on training data**, then applied to validation and test sequences.

---

## 5. Sequence Generation (Sliding Window)

We segment each engine history into sequences of length `W`:

`X (W × F),  y = RUL_target_at_last_cycle`

- `W` = window length (e.g., 30, 50, 100)
    
- `F` = number of selected features
    

This transforms raw logs into time-series structures ready for LSTM/GRU/CNN architectures.

---

## 6. Train / Validation Split

We split sequences into:

- Training set
    
- Validation set (10–20%)
    

Important: splitting respects engine boundaries to avoid leakage.

---

## 7. Test Set Preprocessing (Last Sequence Only Strategy)

Since FD001 uses the **Last Sequence Only** strategy for inference:

### Steps:

1. Load `test_FD001.txt`
    
2. Load `RUL_FD001.txt`
    
3. For each engine in test:
    
    - Extract **only the last sliding window**
        
    - Scale it using the scaler fitted on training
        
    - Use the provided RUL value **directly** as ground truth
        
    - Apply the same cap as training:
        
        `RUL_true = min(RUL_FD001_value, 125)`
        

### Why this strategy?

- Matches NASA’s official evaluation procedure
    
- Avoids reconstructing full degradation curves
    
- Aligned with real-world inference:
    
    > "Predict RUL from the current state only."
    

---

## 8. Final Output Shapes

After preprocessing, the model receives:

### Training

`X_train : (N_train, W, F) y_train : (N_train,) X_val   : (N_val, W, F) y_val   : (N_val,)`

### Testing (Last-sequence Only)

`X_test  : (N_test, W, F)   # one sequence per engine y_test  : (N_test,)`

---

## 9. Preprocessing Summary

`1. Clean dataset (remove weak sensors)
2. Compute RUL_raw 
3. Apply capped RUL_target = min(RUL_raw, 125) 
4. Scale features using MinMaxScaler((-1, 1)) 
5. Generate sliding-window sequences
6. Split train/validation 
7. For test: last-sequence-only + RUL_FD001 capped at 125 
8. Prepare final arrays for model training`