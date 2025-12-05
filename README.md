# Predictive-Maintenance-CMAPSS

Remaining Useful Life (RUL) estimation using a Hybrid CNN–GRU deep learning model trained on the NASA CMAPSS FD001 turbofan engine dataset.

---

## Overview
This project applies deep learning techniques to predict the Remaining Useful Life (RUL) of aerospace engines using multivariate time-series sensor data.  
Several neural network architectures were explored, and a hybrid CNN–GRU–GRU model was selected as the final solution due to its strong predictive accuracy and stability.

A complete technical explanation (preprocessing, architecture comparison, and full analysis) is available in the `docs/` directory.

---

## Final Model Architecture
- **Input:** 30-cycle window × 14 features  
- **Conv1D:** 32 filters + MaxPooling  
- **GRU:** 64 units  
- **GRU:** 32 units  
- **Dense:** 1 (ReLU activation)  
- **Optimizer:** Adam (lr = 0.001)  
- **Loss:** Mean Squared Error  

---

## Results (FD001)
| Metric        | Score   |
|---------------|---------|
| **RMSE**      | 12.97   |
| **R² Score**  | 0.9026  |
| **NASA Score**| 249.93  |

---

## Learning Curve  
![Training vs Validation Loss](images/cnn_gru_best_model_train_val_loss.png)

---

## True vs Predicted RUL  
![True vs Predicted RUL](images/cnn_gru_best_model_true_vs_pred.png)

---

## Repository Structure
