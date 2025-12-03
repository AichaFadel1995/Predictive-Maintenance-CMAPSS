
This document describes the training protocol used to optimize the Hybrid CNN–GRU model in a **robust**, **safe**, and **industry-oriented** manner.

---

## 1. Guiding Principles

The training is governed by two mission-critical objectives:

### **A. Safety (Primary Objective)**

**Goal:** Minimize the NASA Score.

- The NASA metric penalizes **late predictions aggressively**.
    
- Predicting that the engine has more remaining life than it actually has is **dangerous**.
    
- Safety > Accuracy in the context of predictive maintenance.
    

### **B. Generalization**

**Goal:** Ensure robustness on unseen engines.

- The model must capture **universal degradation patterns**,  
    not memorize noise or specific behaviors from the training engines.
    
- This prevents overfitting and guarantees reliability in real deployments.
    

---

## 2. Learning Configuration

### A. Loss Function

**Choice:** Mean Squared Error (MSE)  
**Reason:**

- Although the final objective is safety (NASA Score), MSE provides a **stable gradient** and a **convex error surface**.
    
- Strongly penalizes large errors → the model corrects them quickly.
    

### B. Monitoring Metrics

- **RMSE** (Root Mean Squared Error):  
    Human-interpretable, expressed directly in **number of cycles**.
    
- **NASA Score (Validation):**  
    Checked after each epoch to ensure that lowering MSE does **not** produce unsafe late-life predictions.
    

### C. Optimizer

**Choice:** Adam (Adaptive Moment Estimation)

- Learning rate: **0.001**
    
- Efficient and stable for CNN–RNN architectures.
    
- Adapts learning rates for each weight individually.
    

---

## 3. Execution Hyperparameters

|Parameter|Value|Justification|
|---|---|---|
|**Batch Size**|64|Best balance between gradient stability and computational speed.|
|**Epochs**|50|Theoretical max; actual training stops earlier via EarlyStopping.|
|**Validation Split**|20%|Ensures a clean separation for real-time generalization checks.|

---

## 4. Safety Protocols (Callbacks)

### A. Early Stopping — _Generalization Control_

- **Monitor:** `val_loss`
    
- **Patience:** 10 epochs
    
- **Action:** Stop training when the model stops improving.
    
- **Restoration:** Reloads the **most generalizing** weights.
    

Purpose: Prevents the model from drifting into overfitting.

---

### B. Model Checkpoint — _Safety Optimization_

- **Action:** Save the model only when a new **minimum validation loss** is achieved.
    
- **File:** `best_model.keras`
    
- Ensures the final stored model is the **safest and most accurate** version encountered during training.
    

---

## 5. Training Flow Summary

`1. Initialization:    - Random weight initialization (Glorot Uniform).  2. Epoch Iteration:    a. Forward Pass:       - Training batches (size = 64)       - Loss computed via MSE (on capped RUL: 125)    b. Backpropagation:       - Weights updated using Adam    c. Validation:       - Compute val_loss on 20% held-out data    d. Safety Checks:       - EarlyStopping: "Am I still generalizing?"       - Checkpoint: "Am I safer than before?"  3. Termination:    - Restore the weights of the best-performing epoch.    - Final model = best balance between accuracy and generalization.`