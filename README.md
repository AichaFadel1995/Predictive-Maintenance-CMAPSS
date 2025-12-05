# Predictive-Maintenance-CMAPSS

Remaining Useful Life (RUL) prediction using the **best-performing Hybrid CNN–GRU model** on the NASA CMAPSS FD001 turbofan dataset.

---

## Best Model: Hybrid CNN–GRU (2 Layers)

This architecture achieved the best balance between accuracy, stability, and generalization.

### **Performance Metrics**
| Metric       | Value   |
|--------------|---------|
| **RMSE**     | 12.97   |
| **R² Score** | 0.9026  |
| **NASA Score** | 249.93 |

---

## 1. Training vs Validation Loss

![Training vs Validation Loss](images/cnn_gru_2layers_train_val_loss.png)

---

## 2. Prediction Tracking Across Engines

![Prediction Plot](images/cnn_gru_2layers_prediction_plot.png)

---

## 3. True vs Predicted RUL (Test Set)

![True vs Predicted RUL](images/cnn_gru_best_model_true_vs_pred.png)

---

## Repository Structure

