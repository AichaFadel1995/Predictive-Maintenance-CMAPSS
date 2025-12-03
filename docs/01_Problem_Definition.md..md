

# Problem Definition & Data Description

## 1. Context

This project is based on the **NASA C-MAPSS** dataset, which simulates the **progressive degradation of aircraft turbofan engines**.  
The simulations reproduce the full life cycle of an engine, from normal operation to complete failure, using a high-fidelity thermodynamic model.

## 2. Problem Statement

A degrading engine that is not detected in time can lead to:

- critical failures with safety risks,
    
- unplanned and costly maintenance actions,
    
- reduced operational availability.
    

The challenge is to **anticipate engine failure before it occurs**, enabling optimized maintenance planning and improved reliability.

## 3. Project Objective

The main goal is to develop a **predictive model** capable of estimating the **Remaining Useful Life (RUL)** of an aircraft engine.  
In other words, the task is to predict **how many operating cycles remain before failure**, using **multivariate time-series data** collected from multiple onboard sensors.

## 4. Dataset Description: NASA C-MAPSS

The dataset used in this project comes from a high-fidelity simulation platform developed by NASA. This platform generates realistic run-to-failure trajectories describing the full degradation process of turbofan engines.

The complete dataset is organized into **four scenarios** (FD001 to FD004), each representing different operating conditions and failure modes.

Each scenario contains three files:

- **train_FD00x.txt**  
    Full operational history of several engines, from healthy state to failure (run-to-failure). Used for model training.
    
- **test_FD00x.txt**  
    Partial operational history ending before the actual failure. The model must infer the remaining life.
    
- **RUL_FD00x.txt**  
    Ground-truth RUL values for each engine in the test set. Used only for evaluation.
    

For this project, we focus on **one scenario**  FD001, which will be detailed later in the methodology.