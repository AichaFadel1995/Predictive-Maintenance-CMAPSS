# : Hybrid CNN–GRU

This document describes the hybrid deep learning architecture used for RUL prediction on the NASA C-MAPSS FD001 dataset. The model combines convolutional layers (CNN) for local pattern extraction and GRU layers for temporal modeling.

---

## 1. Input Specification

**Input tensor shape:**

`(Batch, 30, 14)`

- **30** = sequence window length
    
- **14** = selected features (sensor data + operational settings)
    

### Rationale for Window Length (W = 30)

The value **W = 30** results from a balance between physical realism and dataset constraints:

- **Technical constraint:**  
    The shortest test sequence has **31 cycles**.  
    → A window larger than 31 would cause missing-data errors or require padding.
    
- **Physical constraint:**  
    Turbofan degradation evolves slowly.  
    → Very short windows (<15) capture noise rather than the degradation trend.
    

**Final choice:**

`W = 30  → maximizes usable history and ensures compatibility with 100% of FD001 engines`

---

## 2. Architecture Overview

The model is built in three blocks:

1. **CNN block** — local feature extraction + denoising
    
2. **GRU block** — temporal pattern modeling
    
3. **Regression block** — final RUL prediction (positive output)
    

A dropout layer ensures regularization and robustness.

---

## 3. Detailed Layer Description

### 3.1. Block 1 — CNN (Feature Extractor)

- **Conv1D**
    
    - Filters: 32
        
    - Kernel size: 3
        
    - Activation: ReLU
        
- **Role:**
    
    - Detect short-term local patterns
        
    - Smooth high-frequency sensor noise
        
    - Capture micro-anomalies
        
- **MaxPooling1D (pool_size=2)**
    
    - Reduces temporal dimension
        
    - Keeps dominant activations
        

---

### 3.2. Block 2 — GRU (Temporal Modeling)

The model uses **GRU** instead of LSTM to reduce parameter count and avoid overfitting.

- **GRU Layer 1**
    
    - Units: 32
        
    - `return_sequences=True`
        
    - Processes the filtered sequence
        
- **GRU Layer 2**
    
    - Units: 16
        
    - `return_sequences=False`
        
    - Produces a compact temporal representation
        
- **Dropout (0.2)**
    
    - Improves generalization by randomly deactivating units
        

---

### 3.3. Block 3 — Output Layer

- **Dense(1)**
    
- **Activation:** ReLU
    

**Justification:**  
RUL is a **strictly positive physical quantity**, so ReLU prevents negative predictions.

---

## 4. Model Benchmarking: Heavy vs Light Architecture

To select the optimal architecture, two configurations were evaluated:

### A. Heavy Model (Baseline – High Capacity)

- CNN filters: 64
    
- GRU layers: 64 + 64 units
    
- Complexity: ~40k parameters
    
- Strong expressive power but prone to overfitting
    

### B. Light Model (Final Choice – Optimized)

- CNN filters: 32
    
- GRU layers: 32 + 16 units
    
- Complexity: ~10k parameters (≈4× lighter)
    
- Better generalization on FD001
    
- Faster inference and easier deployment
    

**Outcome:**  
The Light model achieved the best balance between accuracy, safety, and complexity.

---

## 5. Training Strategy

Both architectures were trained under identical conditions for a valid comparison.

- **Loss:** Mean Squared Error (MSE)
    
- **Optimizer:** Adam
    
- **Batch size:** 64
    
- **EarlyStopping:**
    
    - Monitor: `val_loss`
        
    - Patience: 10
        
    - Best weights restored automatically
        

This ensures training stops at the point of optimal generalization.

---

## 6. Summary

`Hybrid CNN–GRU Model -------------------- 
- Input: 30-cycle time windows, 14 features 
- CNN: 32 filters + MaxPooling (feature extraction) 
- GRU: 32 → 16 units (temporal modeling) 
- Dropout: 0.2 
- Output: Dense(1) with ReLU (positive RUL) 
- Final selection: Light model (~10k parameters)